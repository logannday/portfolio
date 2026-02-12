<script lang="ts">
	let { visible = false }: { visible: boolean } = $props();

	const complement: Record<string, string> = { A: 'T', T: 'A', G: 'C', C: 'G' };

	// Original 16 base pairs
	const originalBases = [
		'A',
		'T',
		'G',
		'C',
		'A',
		'G',
		'T',
		'C',
		'G',
		'A',
		'T',
		'C',
		'G',
		'T',
		'A',
		'C'
	];

	// Two insertion triplets
	const insertion1 = { bases: ['G', 'A', 'T'], afterIndex: 4 };
	const insertion2 = { bases: ['C', 'T', 'A'], afterIndex: 11 }; // index in original strand

	type BaseEntry = {
		base: string;
		type: 'original' | 'insertion';
		state: 'visible' | 'hidden' | 'entering' | 'settled';
		id: string;
		insertionGroup?: 1 | 2;
	};

	let displayStrand = $state<BaseEntry[]>(
		originalBases.map((b, i) => ({
			base: b,
			type: 'original' as const,
			state: 'visible' as const,
			id: `orig-${i}`
		}))
	);

	let phase = $state<'idle' | 'insert1' | 'insert2' | 'done'>('idle');
	let hasPlayed = $state(false);

	function insertBases(bases: string[], afterIndex: number, group: 1 | 2): BaseEntry[] {
		const newEntries: BaseEntry[] = bases.map((b, i) => ({
			base: b,
			type: 'insertion' as const,
			state: 'hidden' as const,
			id: `ins-${group}-${i}`,
			insertionGroup: group
		}));
		const strand = [...displayStrand];
		strand.splice(afterIndex + 1, 0, ...newEntries);
		return strand;
	}

	function startAnimation() {
		// Phase 1: Insert first triplet (hidden initially)
		setTimeout(() => {
			phase = 'insert1';
			displayStrand = insertBases(insertion1.bases, insertion1.afterIndex, 1);

			// Stagger the reveal of each base
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-1-0' ? { ...b, state: 'entering' } : b
				);
			}, 150);
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-1-1' ? { ...b, state: 'entering' } : b
				);
			}, 300);
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-1-2' ? { ...b, state: 'entering' } : b
				);
			}, 450);

			// Settle after all three appear
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.insertionGroup === 1 ? { ...b, state: 'settled' } : b
				);
			}, 900);
		}, 600);

		// Phase 2: Insert second triplet
		setTimeout(() => {
			phase = 'insert2';
			// insertion2.afterIndex is in original coords (11), but strand now has 3 extra bases
			// inserted after index 4, so real index is 11 + 3 = 14
			displayStrand = insertBases(insertion2.bases, insertion2.afterIndex + 3, 2);

			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-2-0' ? { ...b, state: 'entering' } : b
				);
			}, 150);
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-2-1' ? { ...b, state: 'entering' } : b
				);
			}, 300);
			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.id === 'ins-2-2' ? { ...b, state: 'entering' } : b
				);
			}, 450);

			setTimeout(() => {
				displayStrand = displayStrand.map((b) =>
					b.insertionGroup === 2 ? { ...b, state: 'settled' } : b
				);
			}, 900);
		}, 2200);

		// Done
		setTimeout(() => {
			phase = 'done';
		}, 3600);
	}

	$effect(() => {
		if (visible && !hasPlayed) {
			hasPlayed = true;
			startAnimation();
		}
	});
</script>

<div
	class="overflow-hidden rounded-xl border border-slate-200 bg-white/50 p-4 sm:p-6 dark:border-slate-800 dark:bg-slate-800/30"
>
	<p
		class="mb-3 text-center text-xs font-semibold tracking-wide text-slate-500 dark:text-slate-400"
	>
		Insertion Mutations
	</p>
	<div
		class="flex flex-col items-center gap-0 font-mono text-xs leading-tight sm:text-sm md:text-base"
	>
		<!-- Sense strand (top) -->
		<div class="flex items-center justify-center">
			{#each displayStrand as bp (bp.id)}
				<span
					class="inline-flex justify-center text-center transition-all duration-300 ease-out"
					class:text-primary-600={bp.type === 'original'}
					class:dark:text-primary-400={bp.type === 'original'}
					class:text-accent-500={bp.type === 'insertion'}
					class:dark:text-accent-400={bp.type === 'insertion'}
					class:font-bold={bp.type === 'insertion' && bp.state !== 'hidden'}
					class:dna-glow={bp.state === 'settled'}
					style="width: {bp.state === 'hidden' ? '0' : '1.5ch'}; max-width: {bp.state === 'hidden'
						? '0'
						: '1.5ch'}; opacity: {bp.state === 'hidden' ? '0' : '1'}; overflow: hidden;"
				>
					{bp.base}
				</span>
			{/each}
		</div>

		<!-- Connector pipes -->
		<div class="flex items-center justify-center">
			{#each displayStrand as bp (bp.id)}
				<span
					class="inline-flex justify-center text-center text-slate-400 transition-all duration-300 ease-out dark:text-slate-600"
					style="width: {bp.state === 'hidden' ? '0' : '1.5ch'}; max-width: {bp.state === 'hidden'
						? '0'
						: '1.5ch'}; opacity: {bp.state === 'hidden'
						? '0'
						: bp.state === 'entering'
							? '0.5'
							: '1'}; overflow: hidden;"
				>
					|
				</span>
			{/each}
		</div>

		<!-- Complement strand (bottom) -->
		<div class="flex items-center justify-center">
			{#each displayStrand as bp (bp.id)}
				<span
					class="inline-flex justify-center text-center transition-all duration-300 ease-out"
					class:text-primary-600={bp.type === 'original'}
					class:dark:text-primary-400={bp.type === 'original'}
					class:text-accent-500={bp.type === 'insertion'}
					class:dark:text-accent-400={bp.type === 'insertion'}
					class:font-bold={bp.type === 'insertion' && bp.state !== 'hidden'}
					class:dna-glow={bp.state === 'settled'}
					style="width: {bp.state === 'hidden' ? '0' : '1.5ch'}; max-width: {bp.state === 'hidden'
						? '0'
						: '1.5ch'}; opacity: {bp.state === 'hidden' ? '0' : '1'}; overflow: hidden;"
				>
					{complement[bp.base]}
				</span>
			{/each}
		</div>
	</div>
</div>
