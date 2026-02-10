<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';
	import { page } from '$app/state';

	let { children } = $props();

	let dark = $state(true);
	let scrollY = $state(0);
	let mobileMenuOpen = $state(false);

	let isPortfolio = $derived(page.url.pathname === '/');

	const navLinks = [
		{ label: 'About', href: '#about' },
		{ label: 'Publications', href: '#publications' },
		{ label: 'Projects', href: '#projects' },
		{ label: 'Skills', href: '#skills' },
		{ label: 'Contact', href: '#contact' }
	];

	function toggleTheme() {
		dark = !dark;
	}

	function closeMobileMenu() {
		mobileMenuOpen = false;
	}
</script>

<svelte:window bind:scrollY />

<svelte:head>
	<link rel="icon" href={favicon} />
	<title>Logan Day | CS Researcher & Engineer</title>
	<meta
		name="description"
		content="Portfolio of Logan Day - Software Engineer specializing in bioinformatics, machine learning, and web development."
	/>
</svelte:head>

{#if isPortfolio}
	<div class={dark ? 'dark' : ''}>
		<div class="min-h-screen bg-white text-slate-900 dark:bg-slate-950 dark:text-slate-100">
			<!-- Navigation -->
			<nav
				class="fixed top-0 right-0 left-0 z-50 transition-all duration-300 {scrollY > 50
					? 'border-b border-slate-200 bg-white/80 shadow-sm backdrop-blur-md dark:border-slate-800 dark:bg-slate-950/80'
					: 'bg-transparent'}"
			>
				<div class="mx-auto flex max-w-6xl items-center justify-between px-6 py-4">
					<a
						href="#top"
						class="font-mono text-lg font-medium text-primary-600 transition-colors hover:text-primary-500 dark:text-primary-400 dark:hover:text-primary-300"
					>
						LD
					</a>

					<!-- Desktop nav -->
					<div class="hidden items-center gap-8 md:flex">
						{#each navLinks as link}
							<a
								href={link.href}
								class="text-sm font-medium text-slate-600 transition-colors hover:text-primary-600 dark:text-slate-400 dark:hover:text-primary-400"
							>
								{link.label}
							</a>
						{/each}

						<!-- Theme toggle -->
						<button
							onclick={toggleTheme}
							class="rounded-full p-2 text-slate-500 transition-colors hover:bg-slate-100 hover:text-slate-700 dark:text-slate-400 dark:hover:bg-slate-800 dark:hover:text-slate-200"
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
									viewBox="0 0 24 24"
									fill="currentColor"
								>
									<path
										fill-rule="evenodd"
										d="M9.528 1.718a.75.75 0 0 1 .162.819A8.97 8.97 0 0 0 9 6a9 9 0 0 0 9 9 8.97 8.97 0 0 0 3.463-.69.75.75 0 0 1 .981.98 10.503 10.503 0 0 1-9.694 6.46c-5.799 0-10.5-4.701-10.5-10.5 0-4.368 2.667-8.112 6.46-9.694a.75.75 0 0 1 .818.162Z"
										clip-rule="evenodd"
									/>
								</svg>
							{/if}
						</button>
					</div>

					<!-- Mobile menu button -->
					<div class="flex items-center gap-4 md:hidden">
						<button
							onclick={toggleTheme}
							class="rounded-full p-2 text-slate-500 transition-colors hover:bg-slate-100 dark:text-slate-400 dark:hover:bg-slate-800"
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
									viewBox="0 0 24 24"
									fill="currentColor"
								>
									<path
										fill-rule="evenodd"
										d="M9.528 1.718a.75.75 0 0 1 .162.819A8.97 8.97 0 0 0 9 6a9 9 0 0 0 9 9 8.97 8.97 0 0 0 3.463-.69.75.75 0 0 1 .981.98 10.503 10.503 0 0 1-9.694 6.46c-5.799 0-10.5-4.701-10.5-10.5 0-4.368 2.667-8.112 6.46-9.694a.75.75 0 0 1 .818.162Z"
										clip-rule="evenodd"
									/>
								</svg>
							{/if}
						</button>
						<button
							onclick={() => (mobileMenuOpen = !mobileMenuOpen)}
							class="rounded-lg p-2 text-slate-500 transition-colors hover:bg-slate-100 dark:text-slate-400 dark:hover:bg-slate-800"
							aria-label="Toggle menu"
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								class="h-6 w-6"
								fill="none"
								viewBox="0 0 24 24"
								stroke="currentColor"
							>
								{#if mobileMenuOpen}
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M6 18L18 6M6 6l12 12"
									/>
								{:else}
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M4 6h16M4 12h16M4 18h16"
									/>
								{/if}
							</svg>
						</button>
					</div>
				</div>

				<!-- Mobile menu -->
				{#if mobileMenuOpen}
					<div
						class="border-t border-slate-200 bg-white/95 backdrop-blur-md md:hidden dark:border-slate-800 dark:bg-slate-950/95"
					>
						<div class="flex flex-col gap-1 px-6 py-4">
							{#each navLinks as link}
								<a
									href={link.href}
									onclick={closeMobileMenu}
									class="rounded-lg px-3 py-2 text-sm font-medium text-slate-600 transition-colors hover:bg-slate-100 hover:text-primary-600 dark:text-slate-400 dark:hover:bg-slate-800 dark:hover:text-primary-400"
								>
									{link.label}
								</a>
							{/each}
						</div>
					</div>
				{/if}
			</nav>

			<!-- Main content -->
			<main>
				{@render children()}
			</main>
		</div>
	</div>
{:else}
	{@render children()}
{/if}
