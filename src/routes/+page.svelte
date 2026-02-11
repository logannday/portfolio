<script lang="ts">
	import { onMount } from 'svelte';

	let mounted = $state(false);

	onMount(() => {
		mounted = true;

		// Intersection Observer for scroll reveal animations
		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						entry.target.classList.add('visible');
						observer.unobserve(entry.target);
					}
				});
			},
			{ threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
		);

		document.querySelectorAll('.reveal').forEach((el) => observer.observe(el));

		return () => observer.disconnect();
	});
</script>

<!-- ==================== HERO ==================== -->
<section
	id="top"
	class="relative flex min-h-screen items-center justify-center overflow-hidden px-6"
>
	<!-- Background gradient orbs -->
	<div class="pointer-events-none absolute inset-0 overflow-hidden">
		<div
			class="absolute -top-40 -right-40 h-96 w-96 rounded-full bg-primary-500/10 blur-3xl dark:bg-primary-500/5"
		></div>
		<div
			class="absolute -bottom-40 -left-40 h-96 w-96 rounded-full bg-accent-500/10 blur-3xl dark:bg-accent-500/5"
		></div>
	</div>

	<div class="relative z-10 mx-auto max-w-4xl text-center">
		{#if mounted}
			<div class="animate-fade-in-up">
				<p class="mb-4 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
					CS RESEARCHER & ENGINEER
				</p>
			</div>
			<div class="animate-fade-in-up delay-100">
				<h1
					class="mb-6 text-5xl font-extrabold tracking-tight text-slate-900 sm:text-7xl dark:text-white"
				>
					Logan Day
				</h1>
			</div>
			<div class="animate-fade-in-up delay-200">
				<p
					class="mx-auto mb-8 max-w-2xl text-lg leading-relaxed text-slate-600 dark:text-slate-400"
				>
					Computer Science researcher exploring
					<span class="font-semibold text-primary-600 dark:text-primary-400"
						>structural bioinformatics</span
					>,
					<span class="font-semibold text-accent-500 dark:text-accent-400">machine learning</span>,
					and
					<span class="font-semibold text-emerald-600 dark:text-emerald-400">web development</span>.
				</p>
			</div>
			<div class="animate-fade-in-up flex flex-wrap items-center justify-center gap-4 delay-300">
				<a
					href="#contact"
					class="rounded-full bg-primary-600 px-8 py-3 text-sm font-semibold text-white shadow-lg shadow-primary-500/25 transition-all hover:-translate-y-0.5 hover:bg-primary-500 hover:shadow-xl hover:shadow-primary-500/30 dark:bg-primary-500 dark:shadow-primary-500/20 dark:hover:bg-primary-400"
				>
					Get in Touch
				</a>
			</div>
		{/if}
	</div>

	<!-- Scroll indicator -->
	{#if mounted}
		<div class="animate-fade-in absolute bottom-8 left-1/2 -translate-x-1/2 delay-800">
			<a
				href="#about"
				class="flex flex-col items-center gap-2 text-slate-400 transition-colors hover:text-primary-500 dark:text-slate-600 dark:hover:text-primary-400"
			>
				<span class="text-xs font-medium tracking-wider uppercase">Scroll</span>
				<svg class="h-5 w-5 animate-bounce" fill="none" viewBox="0 0 24 24" stroke="currentColor">
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M19 14l-7 7m0 0l-7-7m7 7V3"
					/>
				</svg>
			</a>
		</div>
	{/if}
</section>

<!-- ==================== ABOUT ==================== -->
<section id="about" class="px-6 py-24">
	<div class="mx-auto max-w-5xl">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				01
			</p>
			<h2 class="mb-12 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">About Me</h2>
		</div>
		<div class="reveal grid items-center gap-12 md:grid-cols-5">
			<!-- Photo -->
			<div class="md:col-span-2">
				<div class="relative">
					<div
						class="absolute -inset-4 rounded-2xl bg-gradient-to-br from-primary-500/20 to-accent-500/20 blur-2xl dark:from-primary-500/10 dark:to-accent-500/10"
					></div>
					<img
						src="/profile.jpeg"
						alt="Logan Day"
						class="relative aspect-[4/5] w-full rounded-2xl border border-slate-200 object-cover shadow-xl dark:border-slate-800"
					/>
				</div>
			</div>
			<!-- Bio -->
			<div class="md:col-span-3">

					I'm a CS master's student and researcher at Western Washington University. I build
					high-throughput pipelines for generating and analyzing exhaustive sets of protein mutant
					structures, and I develop unsupervised anomaly detection methods for DNS exfiltration.
					Outside of research, I enjoy building web tools and keeping up with the JavaScript
					ecosystem.
				</p>
				<p class="mb-8 text-lg leading-relaxed text-slate-600 dark:text-slate-400">
					When I'm not in the lab, I'm usually playing music with friends, outside exploring nature,
					or catastrophizing about the impending AI apocalypse.
				</p>
				<!-- Info row -->
				<div class="grid grid-cols-3 gap-4">
					<div
						class="rounded-xl border border-slate-200 bg-slate-50 p-4 dark:border-slate-800 dark:bg-slate-900"
					>
						<p class="text-xs font-medium text-slate-500 dark:text-slate-500">Location</p>
						<p class="font-semibold text-slate-900 dark:text-white">Washington State</p>
					</div>
					<div
						class="rounded-xl border border-slate-200 bg-slate-50 p-4 dark:border-slate-800 dark:bg-slate-900"
					>
						<p class="text-xs font-medium text-slate-500 dark:text-slate-500">Focus Areas</p>
						<p class="font-semibold text-slate-900 dark:text-white">Bioinformatics, ML, Web Dev</p>
					</div>
					<div
						class="rounded-xl border border-slate-200 bg-slate-50 p-4 dark:border-slate-800 dark:bg-slate-900"
					>
						<p class="text-xs font-medium text-slate-500 dark:text-slate-500">Degree</p>
						<p class="font-semibold text-slate-900 dark:text-white">MSCS (GPA: 3.94)</p>
					</div>
				</div>
			</div>
		</div>
	</div>
</section>

<!-- ==================== RESEARCH PROJECTS (commented out) ====================
<section id="research" class="bg-slate-50 px-6 py-24 dark:bg-slate-900/50">
	<div class="mx-auto max-w-4xl">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				02
			</p>
			<h2 class="mb-12 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">
				Research Projects
			</h2>
		</div>

		<div class="space-y-8">
			<div
				class="reveal group rounded-2xl border border-slate-200 bg-white p-8 shadow-sm transition-all hover:shadow-lg hover:shadow-primary-500/5 dark:border-slate-800 dark:bg-slate-900 dark:hover:shadow-primary-500/5"
			>
				<div class="mb-4 flex flex-wrap items-start justify-between gap-4">
					<div>
						<div class="mb-2 flex items-center gap-3">
							<div
								class="flex h-10 w-10 items-center justify-center rounded-lg bg-primary-100 text-primary-600 dark:bg-primary-900/30 dark:text-primary-400"
							>
								<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
									<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z" />
								</svg>
							</div>
							<h3 class="text-xl font-bold text-slate-900 dark:text-white">
								Structural Analysis of Protein Mutations
							</h3>
						</div>
						<p class="font-mono text-sm text-slate-500 dark:text-slate-500">Jagodinski Research Group</p>
					</div>
				</div>
				<ul class="space-y-3">
					<li class="flex gap-3 text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-primary-500"></span>
						<span>Exhaustively generated and performed structural analysis of protein insertion-deletion (indel) mutants using computational modeling techniques</span>
					</li>
					<li class="flex gap-3 text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-primary-500"></span>
						<span>Proposed a novel metric (&Delta;FA) for quantifying structural changes at protein-protein interaction interfaces</span>
					</li>
					<li class="flex gap-3 text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-primary-500"></span>
						<span>Analyzed the effects of interface distance, inserted residue type, and secondary structure at the insertion site on the &Delta;FA of a protein</span>
					</li>
				</ul>
				<div class="mt-6 flex flex-wrap gap-2">
					{#each ['Rosetta', 'Biopython', 'Structural Biology'] as tag}
						<span class="rounded-full bg-primary-50 px-3 py-1 text-xs font-medium text-primary-700 dark:bg-primary-900/20 dark:text-primary-300">{tag}</span>
					{/each}
				</div>
			</div>

			<div
				class="reveal group rounded-2xl border border-slate-200 bg-white p-8 shadow-sm transition-all hover:shadow-lg hover:shadow-accent-500/5 dark:border-slate-800 dark:bg-slate-900 dark:hover:shadow-accent-500/5"
			>
				<div class="mb-4 flex flex-wrap items-start justify-between gap-4">
					<div>
						<div class="mb-2 flex items-center gap-3">
							<div class="bg-accent-100 dark:bg-accent-900/30 flex h-10 w-10 items-center justify-center rounded-lg text-accent-600 dark:text-accent-400">
								<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
									<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
								</svg>
							</div>
							<h3 class="text-xl font-bold text-slate-900 dark:text-white">
								Network Intrusion Detection System
							</h3>
						</div>
						<p class="font-mono text-sm text-slate-500 dark:text-slate-500">Hong-Jagodzinski Research Group</p>
					</div>
				</div>
				<ul class="space-y-3">
					<li class="flex gap-3 text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-accent-500"></span>
						<span>Developing methods for detecting adversarial DNS exfiltration attacks using random forest, time-series autoencoders, and contrastive learning</span>
					</li>
					<li class="flex gap-3 text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-accent-500"></span>
						<span>Combining multiple ML approaches to build robust anomaly detection for network traffic patterns</span>
					</li>
				</ul>
				<div class="mt-6 flex flex-wrap gap-2">
					{#each ['PyTorch', 'scikit-learn', 'Network Security', 'Contrastive Learning'] as tag}
						<span class="bg-accent-50 text-accent-700 dark:bg-accent-900/20 dark:text-accent-300 rounded-full px-3 py-1 text-xs font-medium">{tag}</span>
					{/each}
				</div>
			</div>
		</div>
	</div>
</section>
==================== -->

<!-- ==================== PUBLICATIONS ==================== -->
<section id="publications" class="bg-slate-50 px-6 py-24 dark:bg-slate-900/50">
	<div class="mx-auto max-w-4xl">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				02
			</p>
			<h2 class="mb-12 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">
				Publications & Presentations
			</h2>
		</div>

		<div class="space-y-6">
			<div
				class="reveal group rounded-xl border-l-4 border-primary-500 bg-slate-50 p-6 transition-all hover:bg-slate-100 dark:bg-slate-900/50 dark:hover:bg-slate-900"
			>
				<div class="mb-2 flex flex-wrap items-center gap-3">
					<span
						class="rounded-full bg-amber-100 px-3 py-1 text-xs font-bold text-amber-800 dark:bg-amber-900/30 dark:text-amber-300"
					>
						Best Presentation (Honorable Mention)
					</span>
					<span class="font-mono text-sm text-slate-500 dark:text-slate-500">2025</span>
				</div>
				<h3 class="mb-2 text-lg font-bold">
					<a
						href="https://dl.acm.org/doi/abs/10.1145/3768322.3769030"
						target="_blank"
						rel="noopener noreferrer"
						class="text-slate-900 transition-colors hover:text-primary-600 dark:text-white dark:hover:text-primary-400"
						>Structural Impacts of Insertion Mutations on Protein-Protein Interfaces</a
					>
				</h3>
				<p class="mb-3 text-sm text-slate-600 dark:text-slate-400">
					Computational Structural Bioinformatics Workshop at ACM-BCB 2025
				</p>
				<ul class="space-y-2">
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-primary-500"></span>
						<span
							>Proposed &Delta;FA, a novel metric for quantifying structural changes at
							protein-protein interaction interfaces caused by insertion mutations</span
						>
					</li>
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-primary-500"></span>
						<span
							>Exhaustively generated and analyzed indel mutants using Rosetta computational
							modeling to study effects of interface distance, residue type, and secondary structure</span
						>
					</li>
				</ul>
			</div>

			<div
				class="reveal group rounded-xl border-l-4 border-accent-500 bg-slate-50 p-6 transition-all hover:bg-slate-100 dark:bg-slate-900/50 dark:hover:bg-slate-900"
			>
				<div class="mb-2 flex flex-wrap items-center gap-3">
					<span
						class="rounded-full bg-sky-100 px-3 py-1 text-xs font-bold text-sky-800 dark:bg-sky-900/30 dark:text-sky-300"
					>
						In Review
					</span>
					<span class="font-mono text-sm text-slate-500 dark:text-slate-500">2026</span>
				</div>
				<h3 class="mb-2 text-lg font-bold text-slate-900 dark:text-white">
					Adversarial DNS Exfiltration: Framework and Defense Evaluation
				</h3>
				<ul class="space-y-2">
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-accent-500"></span>
						<span
							>Developed <span class="font-semibold text-accent-600 dark:text-accent-400"
								>ContraDNS</span
							>, a contrastive learning architecture for stateless DNS anomaly detection that trains
							exclusively on benign traffic</span
						>
					</li>
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-accent-500"></span>
						<span
							>Multi-scale character-level CNN encodes queries into embeddings with DNS-specific
							augmentations; detects attacks as out-of-distribution samples via distance
							thresholding</span
						>
					</li>
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-accent-500"></span>
						<span
							>Cross-dataset evaluation revealed no single detection paradigm catches all attack
							types, demonstrating the need for multi-modal defense strategies</span
						>
					</li>
				</ul>
			</div>
		</div>
	</div>
</section>

<!-- ==================== PROJECTS ==================== -->
<section id="projects" class="px-6 py-24">
	<div class="mx-auto max-w-4xl">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				03
			</p>
			<h2 class="mb-12 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">
				Personal Projects
			</h2>
		</div>

		<div class="reveal space-y-8">
			<div
				class="rounded-2xl border border-slate-200 bg-white p-8 shadow-sm dark:border-slate-800 dark:bg-slate-900"
			>
				<div class="mb-4 flex flex-wrap items-start justify-between gap-4">
					<div>
						<div class="mb-2 flex items-center gap-3">
							<div
								class="flex h-10 w-10 items-center justify-center rounded-lg bg-emerald-100 text-emerald-600 dark:bg-emerald-900/30 dark:text-emerald-400"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									class="h-5 w-5"
									fill="none"
									viewBox="0 0 24 24"
									stroke="currentColor"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"
									/>
								</svg>
							</div>
							<h3 class="text-xl font-bold text-slate-900 dark:text-white">Slab Scraper</h3>
						</div>
						<p class="font-mono text-sm text-slate-500 dark:text-slate-500">Svelte / GraphQL</p>
					</div>
				</div>
				<p class="mb-4 text-slate-600 dark:text-slate-400">
					A tool that scrapes venue websites powered by VenuePilot, collects show information into a
					single events list, and exports the data as CSV. Built for the local online music zine
					<a
						href="https://theslabbellingham.com/"
						target="_blank"
						rel="noopener noreferrer"
						class="font-semibold text-emerald-600 underline decoration-emerald-500/30 transition-colors hover:text-emerald-500 dark:text-emerald-400 dark:decoration-emerald-400/30 dark:hover:text-emerald-300"
						>The Slab</a
					>.
				</p>
				<ul class="mb-6 space-y-2">
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-emerald-500"></span>
						<span
							>Queries VenuePilot's public GraphQL API client-side to aggregate show listings across
							multiple venues</span
						>
					</li>
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-emerald-500"></span>
						<span
							>Discovers new venue IDs by scraping calendar pages via CORS proxy, with localStorage
							persistence</span
						>
					</li>
					<li class="flex gap-3 text-sm text-slate-600 dark:text-slate-400">
						<span class="mt-1.5 h-1.5 w-1.5 flex-shrink-0 rounded-full bg-emerald-500"></span>
						<span>Date filtering and CSV export for easy use in print zine production</span>
					</li>
				</ul>
				<div class="flex flex-wrap items-center gap-2">
					{#each ['Svelte', 'GraphQL', 'LocalStorage', 'CSV Export', 'CORS Proxy'] as tag}
						<span
							class="rounded-full bg-emerald-50 px-3 py-1 text-xs font-medium text-emerald-700 dark:bg-emerald-900/20 dark:text-emerald-300"
							>{tag}</span
						>
					{/each}
					<a
						href="/slab-scraper"
						class="ml-auto inline-flex items-center gap-1.5 rounded-full bg-emerald-600 px-4 py-1.5 text-xs font-semibold text-white transition-all hover:-translate-y-0.5 hover:bg-emerald-500 dark:bg-emerald-500 dark:hover:bg-emerald-400"
					>
						Try it
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-3.5 w-3.5"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M9 5l7 7-7 7"
							/>
						</svg>
					</a>
				</div>
			</div>
		</div>
	</div>
</section>

<!-- ==================== TECHNICAL SKILLS ==================== -->
<section id="skills" class="bg-slate-50 px-6 py-24 dark:bg-slate-900/50">
	<div class="mx-auto max-w-4xl">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				04
			</p>
			<h2 class="mb-12 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">
				Technical Skills
			</h2>
		</div>

		<div class="reveal grid gap-6 sm:grid-cols-2">
			<!-- Specializations -->
			<div
				class="rounded-2xl border border-slate-200 bg-white p-6 transition-all hover:shadow-md dark:border-slate-800 dark:bg-slate-900"
			>
				<div class="mb-4 flex items-center gap-3">
					<div
						class="flex h-10 w-10 items-center justify-center rounded-lg bg-emerald-100 text-emerald-600 dark:bg-emerald-900/30 dark:text-emerald-400"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-5 w-5"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"
							/>
						</svg>
					</div>
					<h3 class="text-lg font-bold text-slate-900 dark:text-white">Specializations</h3>
				</div>
				<div class="flex flex-wrap gap-2">
					{#each ['Structural Bioinformatics', 'Machine Learning', 'Web Development'] as skill}
						<span
							class="rounded-lg bg-emerald-50 px-3 py-1.5 text-sm font-medium text-emerald-700 dark:bg-emerald-900/20 dark:text-emerald-300"
							>{skill}</span
						>
					{/each}
				</div>
			</div>

			<!-- Languages -->
			<div
				class="rounded-2xl border border-slate-200 bg-white p-6 transition-all hover:shadow-md dark:border-slate-800 dark:bg-slate-900"
			>
				<div class="mb-4 flex items-center gap-3">
					<div
						class="flex h-10 w-10 items-center justify-center rounded-lg bg-primary-100 text-primary-600 dark:bg-primary-900/30 dark:text-primary-400"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-5 w-5"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4"
							/>
						</svg>
					</div>
					<h3 class="text-lg font-bold text-slate-900 dark:text-white">Languages</h3>
				</div>
				<div class="flex flex-wrap gap-2">
					{#each ['Python', 'Typescript', 'Rust', 'C/C++', 'R', 'SQL', 'HTML', 'CSS'] as skill}
						<span
							class="rounded-lg bg-primary-50 px-3 py-1.5 text-sm font-medium text-primary-700 dark:bg-primary-900/20 dark:text-primary-300"
							>{skill}</span
						>
					{/each}
				</div>
			</div>

			<!-- Tools & Frameworks -->
			<div
				class="rounded-2xl border border-slate-200 bg-white p-6 transition-all hover:shadow-md dark:border-slate-800 dark:bg-slate-900"
			>
				<div class="mb-4 flex items-center gap-3">
					<div
						class="flex h-10 w-10 items-center justify-center rounded-lg bg-amber-100 text-amber-600 dark:bg-amber-900/30 dark:text-amber-400"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-5 w-5"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.066 2.573c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.573 1.066c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.066-2.573c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"
							/>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"
							/>
						</svg>
					</div>
					<h3 class="text-lg font-bold text-slate-900 dark:text-white">Tools & Frameworks</h3>
				</div>
				<div class="flex flex-wrap gap-2">
					{#each ['Svelte', 'React', 'Tailwind CSS', 'Shadcn', 'Docker', 'PyTorch', 'Scikit-learn', 'Pandas', 'Rosetta', 'Jupyter'] as skill}
						<span
							class="rounded-lg bg-amber-50 px-3 py-1.5 text-sm font-medium text-amber-700 dark:bg-amber-900/20 dark:text-amber-300"
							>{skill}</span
						>
					{/each}
				</div>
			</div>

			<!-- Workflow / Environment -->
			<div
				class="rounded-2xl border border-slate-200 bg-white p-6 transition-all hover:shadow-md dark:border-slate-800 dark:bg-slate-900"
			>
				<div class="mb-4 flex items-center gap-3">
					<div
						class="flex h-10 w-10 items-center justify-center rounded-lg bg-accent-100 text-accent-600 dark:bg-accent-900/30 dark:text-accent-400"
					>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-5 w-5"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M8 9l3 3-3 3m5 0h3M5 20h14a2 2 0 002-2V6a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"
							/>
						</svg>
					</div>
					<h3 class="text-lg font-bold text-slate-900 dark:text-white">Workflow</h3>
				</div>
				<div class="flex flex-wrap gap-2">
					{#each ['Vim', 'Tmux', 'Unix tools'] as skill}
						<span
							class="rounded-lg bg-accent-50 px-3 py-1.5 text-sm font-medium text-accent-700 dark:bg-accent-900/20 dark:text-accent-300"
							>{skill}</span
						>
					{/each}
				</div>
			</div>
		</div>
	</div>
</section>

<!-- ==================== CONTACT ==================== -->
<section id="contact" class="px-6 py-24">
	<div class="mx-auto max-w-4xl text-center">
		<div class="reveal">
			<p class="mb-2 font-mono text-sm tracking-widest text-primary-600 dark:text-primary-400">
				05
			</p>
			<h2 class="mb-4 text-3xl font-bold text-slate-900 sm:text-4xl dark:text-white">
				Get in Touch
			</h2>
			<p class="mx-auto mb-12 max-w-lg text-lg text-slate-600 dark:text-slate-400">
				Interested in collaboration, research, or just want to connect? Feel free to reach out.
			</p>
		</div>

		<div class="reveal flex flex-wrap items-center justify-center gap-4">
			<a
				href="mailto:logannday@gmail.com"
				class="group flex items-center gap-3 rounded-xl border border-slate-200 bg-white px-6 py-4 transition-all hover:-translate-y-0.5 hover:shadow-lg dark:border-slate-800 dark:bg-slate-900"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5 text-primary-500"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"
					/>
				</svg>
				<span
					class="text-sm font-medium text-slate-700 group-hover:text-primary-600 dark:text-slate-300 dark:group-hover:text-primary-400"
					>logannday@gmail.com</span
				>
			</a>

			<a
				href="tel:206-708-5434"
				class="group flex items-center gap-3 rounded-xl border border-slate-200 bg-white px-6 py-4 transition-all hover:-translate-y-0.5 hover:shadow-lg dark:border-slate-800 dark:bg-slate-900"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5 text-emerald-500"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"
					/>
				</svg>
				<span
					class="text-sm font-medium text-slate-700 group-hover:text-emerald-600 dark:text-slate-300 dark:group-hover:text-emerald-400"
					>(206) 708-5434</span
				>
			</a>

			<a
				href="https://www.linkedin.com/in/logan-day-693a691b0/"
				target="_blank"
				rel="noopener noreferrer"
				class="group flex items-center gap-3 rounded-xl border border-slate-200 bg-white px-6 py-4 transition-all hover:-translate-y-0.5 hover:shadow-lg dark:border-slate-800 dark:bg-slate-900"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5 text-blue-600"
					viewBox="0 0 24 24"
					fill="currentColor"
				>
					<path
						d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.065 2.064 2.064 0 112.063 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"
					/>
				</svg>
				<span
					class="text-sm font-medium text-slate-700 group-hover:text-blue-600 dark:text-slate-300 dark:group-hover:text-blue-400"
					>LinkedIn</span
				>
			</a>

			<a
				href="https://github.com/logannday"
				target="_blank"
				rel="noopener noreferrer"
				class="group flex items-center gap-3 rounded-xl border border-slate-200 bg-white px-6 py-4 transition-all hover:-translate-y-0.5 hover:shadow-lg dark:border-slate-800 dark:bg-slate-900"
			>
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-5 w-5 text-slate-800 dark:text-slate-200"
					viewBox="0 0 24 24"
					fill="currentColor"
				>
					<path
						d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"
					/>
				</svg>
				<span
					class="text-sm font-medium text-slate-700 group-hover:text-slate-900 dark:text-slate-300 dark:group-hover:text-white"
					>GitHub</span
				>
			</a>
		</div>
	</div>
</section>

<!-- ==================== FOOTER ==================== -->
<footer class="border-t border-slate-200 px-6 py-8 dark:border-slate-800">
	<div class="mx-auto max-w-4xl text-center">
		<p class="text-sm text-slate-500 dark:text-slate-500">
			Built with <span class="font-semibold text-slate-700 dark:text-slate-300">SvelteKit</span> &
			<span class="font-semibold text-slate-700 dark:text-slate-300">Tailwind CSS</span>
		</p>
	</div>
</footer>
