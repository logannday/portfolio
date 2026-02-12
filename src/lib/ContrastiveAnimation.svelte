<script lang="ts">
	let { visible = false }: { visible: boolean } = $props();

	// Phases: query → branch → embed → cluster → threshold → anomalies → done
	let phase = $state<
		'idle' | 'show' | 'branch' | 'embed' | 'cluster' | 'threshold' | 'anomalies' | 'done'
	>('idle');
	let hasPlayed = $state(false);

	const original = 'mail.server.example.com';
	const masked = 'ma_l.ser_er.ex_mple.com';
	const shuffled = 'server.mail.example.com';
	const negative = 'cdn.tracker.adnetwork.io';

	type Dot = { x: number; y: number; delay: number };

	// Benign dots in the embedding space
	// Positions are % of the embedding area, kept within 25-75% range to avoid clipping
	const benignDots: Dot[] = [
		{ x: 44, y: 40, delay: 0 },
		{ x: 52, y: 35, delay: 60 },
		{ x: 48, y: 55, delay: 120 },
		{ x: 55, y: 45, delay: 150 },
		{ x: 42, y: 48, delay: 200 },
		{ x: 50, y: 42, delay: 250 },
		{ x: 46, y: 58, delay: 300 },
		{ x: 54, y: 52, delay: 330 },
		{ x: 40, y: 42, delay: 380 },
		{ x: 57, y: 38, delay: 420 },
		{ x: 50, y: 50, delay: 460 },
		{ x: 43, y: 54, delay: 500 },
		{ x: 53, y: 44, delay: 540 }
	];

	const anomalyDots: Dot[] = [
		{ x: 14, y: 22, delay: 0 },
		{ x: 82, y: 68, delay: 150 },
		{ x: 78, y: 18, delay: 300 },
		{ x: 18, y: 72, delay: 450 }
	];

	// Where the two augmented views will "land" as dots in the cluster
	const embed1Target = { x: 47, y: 46 };
	const embed2Target = { x: 51, y: 49 };

	const circleCenter = { x: 49, y: 47 };
	const circleSize = 36;

	function startAnimation() {
		setTimeout(() => {
			phase = 'show';
		}, 200);
		setTimeout(() => {
			phase = 'branch';
		}, 1100);
		setTimeout(() => {
			phase = 'embed';
		}, 2400);
		setTimeout(() => {
			phase = 'cluster';
		}, 3400);
		setTimeout(() => {
			phase = 'threshold';
		}, 4400);
		setTimeout(() => {
			phase = 'anomalies';
		}, 5200);
		setTimeout(() => {
			phase = 'done';
		}, 6200);
	}

	$effect(() => {
		if (visible && !hasPlayed) {
			hasPlayed = true;
			startAnimation();
		}
	});

	const phaseIdx = $derived(
		['idle', 'show', 'branch', 'embed', 'cluster', 'threshold', 'anomalies', 'done'].indexOf(phase)
	);
	const pastBranch = $derived(phaseIdx >= 2);
	const pastEmbed = $derived(phaseIdx >= 3);
	const pastCluster = $derived(phaseIdx >= 4);
	const pastThreshold = $derived(phaseIdx >= 5);
	const pastAnomalies = $derived(phaseIdx >= 6);
</script>

<div
	class="overflow-hidden rounded-xl border border-slate-200 bg-white/50 p-4 sm:p-6 dark:border-slate-800 dark:bg-slate-800/30"
>
	<!-- PART 1: Augmentation (top area) -->
	<div
		class="flex flex-col items-center gap-2 font-mono text-[0.65rem] transition-all duration-700 sm:text-xs"
		style="max-height: {pastEmbed ? '0px' : '200px'}; opacity: {pastEmbed
			? 0
			: 1}; overflow: hidden;"
	>
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

	<!-- Transition arrow between augmentation and embedding space -->
	<div
		class="flex justify-center overflow-hidden transition-all duration-500"
		style="max-height: {phase === 'embed' ? '32px' : '0px'}; opacity: {phase === 'embed' ? 1 : 0};"
	>
		<svg class="h-6 w-6 text-slate-400 dark:text-slate-500" viewBox="0 0 24 24" fill="none">
			<path
				d="M12 4 L12 18 M12 18 L8 14 M12 18 L16 14"
				stroke="currentColor"
				stroke-width="1.5"
				stroke-linecap="round"
				stroke-linejoin="round"
			/>
		</svg>
	</div>

	<!-- PART 2: Embedding space (bottom area) -->
	<div
		class="relative w-full transition-all duration-700 ease-out"
		style="height: {pastEmbed ? '350px' : '0px'}; opacity: {pastEmbed ? 1 : 0}; overflow: hidden;"
	>
		<!-- Threshold circle -->
		<div
			class="absolute rounded-full border-2 border-dashed border-slate-300 transition-all duration-700 ease-out dark:border-slate-600"
			style="
				left: {circleCenter.x}%;
				top: {circleCenter.y}%;
				width: {circleSize}%;
				aspect-ratio: 1;
				transform: translate(-50%, -50%) scale({pastThreshold ? 1 : 0});
				opacity: {pastThreshold ? 1 : 0};
			"
		></div>

		<!-- The two augmented views becoming dots -->
		<div
			class="absolute h-2.5 w-2.5 rounded-full bg-accent-500 transition-all duration-700 ease-out sm:h-3 sm:w-3 dark:bg-accent-400"
			style="
				left: {embed1Target.x}%;
				top: {embed1Target.y}%;
				transform: translate(-50%, -50%) scale({phase === 'embed' ? 1.8 : 1});
				opacity: {pastEmbed ? 1 : 0};
				box-shadow: {phase === 'embed' ? '0 0 8px var(--color-accent-400)' : 'none'};
			"
		></div>

		<div
			class="absolute h-2.5 w-2.5 rounded-full bg-accent-500 transition-all duration-700 ease-out sm:h-3 sm:w-3 dark:bg-accent-400"
			style="
				left: {embed2Target.x}%;
				top: {embed2Target.y}%;
				transform: translate(-50%, -50%) scale({phase === 'embed' ? 1.8 : 1});
				opacity: {pastEmbed ? 1 : 0};
				box-shadow: {phase === 'embed' ? '0 0 8px var(--color-accent-400)' : 'none'};
			"
		></div>

		<!-- Rest of benign cluster -->
		{#each benignDots as dot}
			<div
				class="absolute h-2 w-2 rounded-full bg-accent-500 sm:h-2.5 sm:w-2.5 dark:bg-accent-400"
				style="
					left: {dot.x}%;
					top: {dot.y}%;
					transform: translate(-50%, -50%) scale({pastCluster ? 1 : 0});
					transition: opacity 0.4s ease-out {dot.delay}ms, transform 0.4s ease-out {dot.delay}ms;
					opacity: {pastCluster ? 1 : 0};
				"
			></div>
		{/each}

		<!-- Anomaly dots -->
		{#each anomalyDots as dot}
			<div
				class="absolute h-2 w-2 rounded-full bg-red-500 sm:h-2.5 sm:w-2.5 dark:bg-red-400"
				style="
					left: {dot.x}%;
					top: {dot.y}%;
					transform: translate(-50%, -50%) scale({pastAnomalies ? 1 : 0});
					transition: opacity 0.4s ease-out {dot.delay}ms, transform 0.4s ease-out {dot.delay}ms;
					opacity: {pastAnomalies ? 1 : 0};
				"
			></div>
		{/each}
	</div>
</div>
