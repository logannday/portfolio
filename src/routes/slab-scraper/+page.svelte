<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';

	// --- Types ---
	interface Venue {
		name: string;
		accountId: number;
		url?: string;
	}

	interface EventItem {
		id: string;
		name: string;
		date: string;
		doorTime: string;
		startTime: string;
		endTime: string;
		venue: string;
		location: string;
		ageRestriction: string;
		promoter: string;
		support: string;
		description: string;
	}

	// --- Config ---
	const GRAPHQL_URL = 'https://www.venuepilot.co/graphql';
	const STORAGE_KEY = 'bellingham-shows-venues';
	const CORS_PROXY = 'https://api.allorigins.win/raw?url=';
	const DEFAULT_VENUES: Venue[] = [
		{ name: 'The Shakedown', accountId: 187 },
		{ name: 'Wild Buffalo', accountId: 191 }
	];

	const EVENTS_QUERY = `
		query ($accountIds: [Int!]!, $startDate: String!, $endDate: String, $search: String, $searchScope: String, $limit: Int, $page: Int) {
			paginatedEvents(arguments: {accountIds: $accountIds, startDate: $startDate, endDate: $endDate, search: $search, searchScope: $searchScope, limit: $limit, page: $page}) {
				collection {
					id name date doorTime startTime endTime minimumAge promoter support description
					venue { id name city state }
				}
				metadata { totalCount totalPages currentPage limitValue }
			}
		}`;

	// --- State ---
	let dark = $state(true);
	let mounted = $state(false);

	let venues: Venue[] = $state([]);
	let allEvents: EventItem[] = $state([]);
	let venueUrl = $state('');
	let discovering = $state(false);
	let discoverStatus = $state<{ text: string; type: 'success' | 'error' | 'loading' } | null>(null);
	let loadingEvents = $state(false);
	let loadError = $state('');
	let activeFilter = $state('all');
	let filterFrom = $state('');
	let filterTo = $state('');

	// --- Derived ---
	let filteredEvents = $derived.by(() => {
		if (activeFilter === 'all') return allEvents;

		const now = new Date();
		now.setHours(0, 0, 0, 0);
		let from: Date | null = null;
		let to: Date | null = null;

		if (activeFilter === 'week') {
			from = new Date(now);
			to = new Date(now);
			to.setDate(to.getDate() + (6 - now.getDay()));
		} else if (activeFilter === 'month') {
			from = new Date(now);
			to = new Date(now.getFullYear(), now.getMonth() + 1, 0);
		} else if (activeFilter === 'next-month') {
			from = new Date(now.getFullYear(), now.getMonth() + 1, 1);
			to = new Date(now.getFullYear(), now.getMonth() + 2, 0);
		} else if (activeFilter === 'custom') {
			from = filterFrom ? new Date(filterFrom + 'T00:00:00') : null;
			to = filterTo ? new Date(filterTo + 'T23:59:59') : null;
		}

		return allEvents.filter((e) => {
			const d = new Date(e.date + 'T12:00:00');
			if (from && d < from) return false;
			if (to && d > to) return false;
			return true;
		});
	});

	let groupedEvents = $derived.by(() => {
		const groups: Record<string, EventItem[]> = {};
		for (const e of filteredEvents) {
			if (!groups[e.date]) groups[e.date] = [];
			groups[e.date].push(e);
		}
		return Object.entries(groups);
	});

	// --- Venue Management ---
	function getVenues(): Venue[] {
		if (!browser) return DEFAULT_VENUES;
		const stored = localStorage.getItem(STORAGE_KEY);
		if (stored) return JSON.parse(stored);
		localStorage.setItem(STORAGE_KEY, JSON.stringify(DEFAULT_VENUES));
		return [...DEFAULT_VENUES];
	}

	function saveVenues(v: Venue[]) {
		if (browser) localStorage.setItem(STORAGE_KEY, JSON.stringify(v));
	}

	function removeVenue(accountId: number) {
		venues = venues.filter((v) => v.accountId !== accountId);
		saveVenues(venues);
		if (allEvents.length > 0) fetchEvents();
	}

	// --- Venue Discovery ---
	async function discoverVenue() {
		const url = venueUrl.trim();
		if (!url) return;

		discovering = true;
		discoverStatus = { text: 'Looking up venue...', type: 'loading' };

		try {
			const pageUrl = url.replace(/\/$/, '').replace(/#.*$/, '');
			const res = await fetch(CORS_PROXY + encodeURIComponent(pageUrl));

			if (!res.ok) throw new Error('Could not reach that URL');

			const html = await res.text();

			const idMatch = html.match(/accountIds\s*:\s*\[(\d+)\]/);
			if (!idMatch) {
				throw new Error(
					'No VenuePilot calendar found on that page. Make sure the URL points to a venue that uses VenuePilot.'
				);
			}

			const accountId = parseInt(idMatch[1]);

			// Extract venue name from page title
			const titleMatch = html.match(/<title[^>]*>([^<]+)<\/title>/i);
			let name = titleMatch ? titleMatch[1].trim() : '';
			name = name.split(/\s*[|–—]\s*/)[0].trim();
			if (!name) name = 'Venue ' + accountId;

			if (venues.some((v) => v.accountId === accountId)) {
				discoverStatus = { text: name + ' is already in your list.', type: 'error' };
				discovering = false;
				return;
			}

			venues = [...venues, { name, accountId, url: pageUrl }];
			saveVenues(venues);

			discoverStatus = { text: 'Added ' + name + '!', type: 'success' };
			venueUrl = '';

			fetchEvents();
		} catch (err: unknown) {
			const message = err instanceof Error ? err.message : 'Unknown error';
			discoverStatus = { text: message, type: 'error' };
		}

		discovering = false;
	}

	// --- Fetch Events ---
	async function fetchEvents() {
		if (venues.length === 0) return;

		loadingEvents = true;
		loadError = '';

		const now = new Date();
		const startDate = formatDate(now);
		const end = new Date(now.getFullYear(), now.getMonth() + 3, 0);
		const endDate = formatDate(end);

		try {
			const res = await fetch(GRAPHQL_URL, {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify({
					operationName: null,
					variables: {
						accountIds: venues.map((v) => v.accountId),
						startDate,
						endDate,
						search: '',
						searchScope: '',
						page: 1,
						limit: 100
					},
					query: EVENTS_QUERY
				})
			});

			const json = await res.json();

			if (json.errors) {
				throw new Error(json.errors.map((e: { message: string }) => e.message).join(', '));
			}

			const collection = json.data.paginatedEvents.collection;
			allEvents = collection
				.map((event: Record<string, unknown>) => {
					const venue = (event.venue as Record<string, string>) || {};
					return {
						id: event.id,
						name: event.name,
						date: event.date,
						doorTime: event.doorTime,
						startTime: event.startTime,
						endTime: event.endTime,
						venue: venue.name || '',
						location: [venue.city, venue.state].filter(Boolean).join(', '),
						ageRestriction: event.minimumAge ? event.minimumAge + '+' : 'All Ages',
						promoter: event.promoter || '',
						support: event.support || '',
						description: event.description || ''
					} as EventItem;
				})
				.filter((e: EventItem) => {
					const d = new Date(e.date);
					const today = new Date();
					today.setHours(0, 0, 0, 0);
					return d >= today;
				})
				.sort(
					(a: EventItem, b: EventItem) => new Date(a.date).getTime() - new Date(b.date).getTime()
				);
		} catch (err: unknown) {
			const message = err instanceof Error ? err.message : 'Unknown error';
			loadError = 'Failed to load events: ' + message;
		}

		loadingEvents = false;
	}

	// --- CSV Export ---
	function exportCsv() {
		if (filteredEvents.length === 0) return;
		const headers = ['Date', 'Day', 'Venue', 'Event', 'Doors', 'Show', 'Age', 'Support'];
		const rows = filteredEvents.map((e) => {
			const d = new Date(e.date + 'T12:00:00');
			const day = d.toLocaleDateString('en-US', { weekday: 'short' });
			return [e.date, day, e.venue, e.name, e.doorTime, e.startTime, e.ageRestriction, e.support];
		});

		const csv = [headers.join(','), ...rows.map((row) => row.map(csvEscape).join(','))].join('\n');

		const blob = new Blob([csv], { type: 'text/csv' });
		const url = URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = 'bellingham-shows.csv';
		a.click();
		URL.revokeObjectURL(url);
	}

	// --- Helpers ---
	function formatDate(d: Date): string {
		return (
			d.getFullYear() +
			'-' +
			String(d.getMonth() + 1).padStart(2, '0') +
			'-' +
			String(d.getDate()).padStart(2, '0')
		);
	}

	function formatTime(t: string): string {
		if (!t) return '--';
		const parts = t.split(':');
		if (parts.length < 2) return t;
		let h = parseInt(parts[0]);
		const m = parts[1];
		const ampm = h >= 12 ? 'pm' : 'am';
		if (h > 12) h -= 12;
		if (h === 0) h = 12;
		return h + ':' + m + ampm;
	}

	function formatDateLabel(dateStr: string): string {
		const d = new Date(dateStr + 'T12:00:00');
		return d.toLocaleDateString('en-US', {
			weekday: 'long',
			month: 'long',
			day: 'numeric'
		});
	}

	function csvEscape(field: string): string {
		const s = String(field || '');
		if (s.includes(',') || s.includes('"') || s.includes('\n')) {
			return '"' + s.replace(/"/g, '""') + '"';
		}
		return s;
	}

	function toggleTheme() {
		dark = !dark;
	}

	const filters = [
		{ id: 'all', label: 'All' },
		{ id: 'week', label: 'This Week' },
		{ id: 'month', label: 'This Month' },
		{ id: 'next-month', label: 'Next Month' },
		{ id: 'custom', label: 'Custom' }
	];

	// --- Init ---
	onMount(() => {
		venues = getVenues();
		mounted = true;
		fetchEvents();
	});
</script>

<div class={dark ? 'dark' : ''}>
	<div
		class="min-h-screen bg-white text-slate-900 transition-colors duration-300 dark:bg-slate-950 dark:text-slate-100"
	>
		<!-- Back button -->
		<a
			href="/"
			class="fixed top-5 left-5 z-50 flex items-center gap-2 rounded-full border border-slate-200 bg-white/80 px-4 py-2 text-sm font-medium text-slate-600 shadow-sm backdrop-blur-md transition-all hover:-translate-x-0.5 hover:border-primary-300 hover:text-primary-600 dark:border-slate-700 dark:bg-slate-900/80 dark:text-slate-400 dark:hover:border-primary-600 dark:hover:text-primary-400"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				class="h-4 w-4"
				fill="none"
				viewBox="0 0 24 24"
				stroke="currentColor"
			>
				<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
			</svg>
			Portfolio
		</a>

		<!-- Theme toggle -->
		<button
			onclick={toggleTheme}
			class="fixed top-5 right-5 z-50 rounded-full border border-slate-200 bg-white/80 p-2.5 text-slate-500 shadow-sm backdrop-blur-md transition-colors hover:bg-slate-100 hover:text-slate-700 dark:border-slate-700 dark:bg-slate-900/80 dark:text-slate-400 dark:hover:bg-slate-800 dark:hover:text-slate-200"
			aria-label="Toggle theme"
		>
			{#if dark}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5"
					viewBox="0 0 20 20"
					fill="currentColor"
				>
					<path
						fill-rule="evenodd"
						d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z"
						clip-rule="evenodd"
					/>
				</svg>
			{:else}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5"
					viewBox="0 0 20 20"
					fill="currentColor"
				>
					<path d="M17.293 13.293A8 8 0 716.707 2.707a8.001 8.001 0 1010.586 10.586z" />
				</svg>
			{/if}
		</button>

		{#if mounted}
			<div class="mx-auto max-w-3xl px-6 py-20">
				<!-- Header -->
				<div class="animate-fade-in-up mb-10 text-center">
					<h1
						class="mb-2 text-4xl font-extrabold tracking-tight text-slate-900 sm:text-5xl dark:text-white"
					>
						Slab Scraper
					</h1>
					<p class="text-lg text-slate-500 dark:text-slate-400">Upcoming concerts and events</p>
				</div>

				<!-- Add Venue -->
				<div
					class="animate-fade-in-up mb-6 rounded-2xl border border-slate-200 bg-white p-6 shadow-sm delay-100 dark:border-slate-800 dark:bg-slate-900"
				>
					<h3
						class="mb-3 text-sm font-semibold tracking-widest text-slate-400 uppercase dark:text-slate-500"
					>
						Add a Venue
					</h3>
					<div class="flex gap-3">
						<input
							type="text"
							bind:value={venueUrl}
							placeholder="Paste a venue calendar URL (e.g. https://wildbuffalo.net/#/calendar)"
							onkeydown={(e) => {
								if (e.key === 'Enter') discoverVenue();
							}}
							class="flex-1 rounded-lg border border-slate-300 bg-white px-4 py-2.5 text-sm text-slate-900 placeholder:text-slate-400 focus:border-emerald-500 focus:ring-1 focus:ring-emerald-500 focus:outline-none dark:border-slate-700 dark:bg-slate-800 dark:text-white dark:placeholder:text-slate-500 dark:focus:border-emerald-400 dark:focus:ring-emerald-400"
						/>
						<button
							onclick={discoverVenue}
							disabled={discovering}
							class="rounded-lg bg-emerald-600 px-5 py-2.5 text-sm font-semibold text-white transition-colors hover:bg-emerald-500 disabled:cursor-not-allowed disabled:opacity-50 dark:bg-emerald-500 dark:hover:bg-emerald-400"
						>
							{discovering ? 'Adding...' : 'Add'}
						</button>
					</div>
					{#if discoverStatus}
						<p
							class="mt-2 text-sm font-medium {discoverStatus.type === 'success'
								? 'text-emerald-600 dark:text-emerald-400'
								: discoverStatus.type === 'error'
									? 'text-red-500 dark:text-red-400'
									: 'text-amber-500 dark:text-amber-400'}"
						>
							{discoverStatus.text}
						</p>
					{/if}
				</div>

				<!-- Active Venues -->
				{#if venues.length > 0}
					<div class="animate-fade-in-up mb-6 flex flex-wrap gap-2 delay-100">
						{#each venues as venue (venue.accountId)}
							<span
								class="inline-flex items-center gap-1.5 rounded-full border border-slate-200 bg-slate-50 py-1.5 pr-2 pl-3.5 text-sm text-slate-700 dark:border-slate-700 dark:bg-slate-800 dark:text-slate-300"
							>
								{venue.name}
								<button
									onclick={() => removeVenue(venue.accountId)}
									class="flex h-5 w-5 items-center justify-center rounded-full text-slate-400 transition-colors hover:bg-red-100 hover:text-red-500 dark:hover:bg-red-900/30 dark:hover:text-red-400"
									title="Remove {venue.name}"
								>
									&times;
								</button>
							</span>
						{/each}
					</div>
				{/if}

				<!-- Controls -->
				<div class="animate-fade-in-up mb-6 flex gap-3 delay-200">
					<button
						onclick={fetchEvents}
						disabled={loadingEvents || venues.length === 0}
						class="flex-1 rounded-lg bg-emerald-600 px-5 py-3 text-sm font-semibold text-white transition-colors hover:bg-emerald-500 disabled:cursor-not-allowed disabled:opacity-50 dark:bg-emerald-500 dark:hover:bg-emerald-400"
					>
						{loadingEvents ? 'Loading...' : 'Load Events'}
					</button>
					<button
						onclick={exportCsv}
						disabled={filteredEvents.length === 0}
						class="rounded-lg border border-emerald-600 px-5 py-3 text-sm font-semibold text-emerald-600 transition-colors hover:bg-emerald-50 disabled:cursor-not-allowed disabled:opacity-50 dark:border-emerald-400 dark:text-emerald-400 dark:hover:bg-emerald-900/20"
					>
						Download CSV
					</button>
				</div>

				<!-- Date Filter -->
				{#if allEvents.length > 0}
					<div class="mb-6 flex flex-wrap items-center gap-3">
						<div class="flex flex-wrap gap-2">
							{#each filters as f}
								<button
									onclick={() => (activeFilter = f.id)}
									class="rounded-full border px-3.5 py-1.5 text-xs font-medium transition-all {activeFilter ===
									f.id
										? 'border-emerald-500 bg-emerald-600 text-white dark:bg-emerald-500'
										: 'border-slate-300 text-slate-500 hover:border-emerald-400 hover:text-slate-700 dark:border-slate-700 dark:text-slate-400 dark:hover:border-emerald-500 dark:hover:text-slate-300'}"
								>
									{f.label}
								</button>
							{/each}
						</div>
						{#if activeFilter === 'custom'}
							<div class="flex items-center gap-2">
								<input
									type="date"
									bind:value={filterFrom}
									class="rounded-lg border border-slate-300 bg-white px-3 py-1.5 text-xs text-slate-700 dark:border-slate-700 dark:bg-slate-800 dark:text-slate-300"
								/>
								<span class="text-xs text-slate-400">to</span>
								<input
									type="date"
									bind:value={filterTo}
									class="rounded-lg border border-slate-300 bg-white px-3 py-1.5 text-xs text-slate-700 dark:border-slate-700 dark:bg-slate-800 dark:text-slate-300"
								/>
							</div>
						{/if}
					</div>
				{/if}

				<!-- Event Count -->
				{#if allEvents.length > 0 && !loadingEvents}
					<p class="mb-4 text-sm text-slate-400 dark:text-slate-500">
						{filteredEvents.length} upcoming show{filteredEvents.length === 1 ? '' : 's'}
					</p>
				{/if}

				<!-- Events -->
				<div class="animate-fade-in-up delay-200">
					{#if loadingEvents}
						<div class="py-16 text-center">
							<div
								class="mb-3 inline-block h-6 w-6 animate-spin rounded-full border-2 border-slate-300 border-t-emerald-500"
							></div>
							<p class="text-sm text-slate-400 dark:text-slate-500">Loading events...</p>
						</div>
					{:else if loadError}
						<div class="py-16 text-center">
							<p class="text-sm text-red-500 dark:text-red-400">{loadError}</p>
						</div>
					{:else if allEvents.length === 0}
						<div class="py-16 text-center">
							<p class="text-sm text-slate-400 dark:text-slate-500">
								Click "Load Events" to see upcoming shows
							</p>
						</div>
					{:else if filteredEvents.length === 0}
						<div class="py-16 text-center">
							<p class="text-sm text-slate-400 dark:text-slate-500">No events match this filter</p>
						</div>
					{:else}
						{#each groupedEvents as [date, events]}
							<div class="mb-8">
								<div
									class="sticky top-0 z-10 mb-3 border-b border-slate-200 bg-white/80 py-2 text-xs font-semibold tracking-wider text-emerald-600 uppercase backdrop-blur-sm dark:border-slate-800 dark:bg-slate-950/80 dark:text-emerald-400"
								>
									{formatDateLabel(date)}
								</div>
								{#each events as event}
									<div
										class="mb-2 rounded-xl border-l-3 border-emerald-500 bg-slate-50 p-4 dark:bg-slate-900/70"
									>
										<p class="font-semibold text-slate-900 dark:text-white">{event.name}</p>
										<div
											class="mt-1.5 flex flex-wrap gap-x-4 gap-y-1 text-sm text-slate-500 dark:text-slate-400"
										>
											<span
												><span class="text-slate-400 dark:text-slate-600">Doors</span>
												{formatTime(event.doorTime)}</span
											>
											<span
												><span class="text-slate-400 dark:text-slate-600">Show</span>
												{formatTime(event.startTime)}</span
											>
											<span class="font-medium text-emerald-600 dark:text-emerald-400"
												>{event.venue}</span
											>
											<span>{event.ageRestriction}</span>
										</div>
										{#if event.support}
											<p class="mt-1.5 text-sm text-slate-400 italic dark:text-slate-500">
												w/ {event.support}
											</p>
										{/if}
									</div>
								{/each}
							</div>
						{/each}
					{/if}
				</div>
			</div>
		{/if}
	</div>
</div>
