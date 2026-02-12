<script lang="ts">
	let { visible = false }: { visible: boolean } = $props();

	let phase = $state<'idle' | 'show' | 'branch' | 'done'>('idle');
	let hasPlayed = $state(false);

	const original = 'mail.server.example.com';
	const masked = 'ma_l.ser_er.ex_mple.com';
	const shuffled = 'server.mail.example.com';

	function startAnimation() {
		setTimeout(() => {
			phase = 'show';
		}, 200);
		setTimeout(() => {
			phase = 'branch';
		}, 1100);
		setTimeout(() => {
			phase = 'done';
		}, 2400);
	}

	$effect(() => {
		if (visible && !hasPlayed) {
			hasPlayed = true;
			startAnimation();
		}
	});

	const pastBranch = $derived(phase === 'branch' || phase === 'done');
</script>

<div
	class="overflow-hidden rounded-xl border border-slate-200 bg-white/50 p-4 sm:p-6 dark:border-slate-800 dark:bg-slate-800/30"
>
	<p
		class="mb-3 text-center text-xs font-semibold tracking-wide text-slate-500 dark:text-slate-400"
	>
		Building Domain Embeddings
	</p>
	<div class="flex flex-col items-center gap-2 font-mono text-[0.65rem] sm:text-xs">
		<!-- Original query -->
		<div
			class="rounded-md border px-3 py-1.5 transition-all duration-500 ease-out"
			style="opacity: {phase === 'idle' ? 0 : pastBranch ? 0.4 : 1};
				border-color: {phase === 'idle' ? 'transparent' : ''};"
			class:border-slate-300={phase !== 'idle'}
			class:dark:border-slate-600={phase !== 'idle'}
		>
			<span class="text-accent-600 dark:text-accent-400">{original}</span>
		</div>

		<!-- Branching views -->
		<div
			class="flex w-full items-start justify-center gap-4 transition-all duration-500 sm:gap-8"
			style="opacity: {pastBranch ? 1 : 0}; transform: translateY({pastBranch ? '0' : '-8px'});"
		>
			<!-- View 1: Mask -->
			<div class="flex flex-col items-center gap-1">
				<span class="text-[0.55rem] text-slate-400 sm:text-[0.65rem] dark:text-slate-500">mask</span
				>
				<svg class="h-3 w-3 text-slate-300 dark:text-slate-600" viewBox="0 0 16 16" fill="none">
					<path
						d="M8 2 L4 10 M8 2 L8 10"
						stroke="currentColor"
						stroke-width="1.5"
						stroke-linecap="round"
					/>
				</svg>
				<div
					class="rounded-md border border-accent-500/40 bg-accent-50 px-2 py-1 dark:border-accent-400/30 dark:bg-accent-900/20"
				>
					<span class="text-accent-600 dark:text-accent-400">
						{#each masked.split('') as char}{#if char === '_'}<span class="font-bold text-red-400"
									>_</span
								>{:else}{char}{/if}{/each}
					</span>
				</div>
			</div>

			<!-- View 2: Shuffle -->
			<div class="flex flex-col items-center gap-1">
				<span class="text-[0.55rem] text-slate-400 sm:text-[0.65rem] dark:text-slate-500"
					>shuffle</span
				>
				<svg class="h-3 w-3 text-slate-300 dark:text-slate-600" viewBox="0 0 16 16" fill="none">
					<path
						d="M8 2 L12 10 M8 2 L8 10"
						stroke="currentColor"
						stroke-width="1.5"
						stroke-linecap="round"
					/>
				</svg>
				<div
					class="rounded-md border border-accent-500/40 bg-accent-50 px-2 py-1 dark:border-accent-400/30 dark:bg-accent-900/20"
				>
					<span class="text-accent-600 dark:text-accent-400">{shuffled}</span>
				</div>
			</div>
		</div>
	</div>
</div>
