<script lang="ts">
	import { onMount } from 'svelte'
	import PostMeta from '$lib/components/PostMeta.svelte'
	import { meta } from './meta'
	import Signatory from './signatory.svelte'
	export let data

	const { signatories, totalCount } = data
	const { title, description, date } = meta

	// Variable to control how many signatories are shown
	const shortListN = 5
	let showAll = false
	let expandAllBios = false
	// Reactive variable to determine the list of signatories to display
	$: visibleSignatories = showAll ? signatories : signatories.slice(0, shortListN)
	// Function to toggle between limited and full list and bios
	function toggleShowAll() {
		showAll = !showAll
		expandAllBios = showAll
	}

	// Milestone goals for signatures
	const milestones = [25, 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 1000, 1500]
	// Find the next milestone goal
	const nextGoal = milestones.find((goal) => totalCount < goal) || milestones[milestones.length - 1]

	// Load Tally script after component mounts
	onMount(() => {
		const d = document
		const w = 'https://tally.so/widgets/embed.js'
		const v = function () {
			if (typeof window.Tally !== 'undefined') {
				window.Tally.loadEmbeds()
			} else {
				d.querySelectorAll('iframe[data-tally-src]:not([src])').forEach((e) => {
					e.src = e.dataset.tallySrc
				})
			}
		}

		if (typeof window.Tally !== 'undefined') {
			v()
		} else if (d.querySelector(`script[src="${w}"]`) === null) {
			const s = d.createElement('script')
			s.src = w
			s.onload = v
			s.onerror = v
			d.body.appendChild(s)
		}
	})
</script>

<PostMeta {title} {description} {date} />

<h1>{title}</h1>

<blockquote class="statement">
	"We call on the governments of the world to sign an international treaty implementing a temporary
	pause on the training of the most powerful general AI systems, until we know how to build them
	safely and keep them under democratic control."
</blockquote>

<!-- Signatories Counter and Goal -->
<div class="signatories-counter">
	<p>We've collected {totalCount} signatures so far— help us reach our first {nextGoal}!</p>
</div>

<!-- Tally Form Container -->
<div class="tally-form-container">
	<iframe
		data-tally-src="https://tally.so/embed/315xdg?alignLeft=1&hideTitle=1&dynamicHeight=1"
		loading="lazy"
		width="100%"
		height="499"
		frameborder="0"
		marginheight="0"
		marginwidth="0"
		title="Sign the statement (verification required)"
	></iframe>
</div>

<div class="signatories-header">
	<h2>Signatories ({totalCount})</h2>
	<button class="expand-all" on:click={toggleShowAll} aria-label="Expand all signatories and bios">
		{showAll ? 'Collapse All' : 'Expand All'}
	</button>
</div>

<section data-pagefind-ignore>
	{#if visibleSignatories.length === 0}
		<p>No signatories found</p>
	{/if}
	<ul class="signatories">
		{#each visibleSignatories as { name, country, bio }}
			<Signatory {name} {country} {bio} {expandAllBios} />
		{/each}
	</ul>

	<!-- Button to toggle between limited and full list -->
	<button on:click={toggleShowAll}>
		{showAll ? 'Show Less' : 'Show All Signatories'}
	</button>
</section>

<style>
	/* Style for the statement */
	.statement {
		margin: 2rem 0;
		padding: 1rem;
		font-weight: normal;
		border-left: 4px solid var(--brand);
		background-color: var(--text-subtle);
		font-style: italic;
		font-size: 1rem;
		line-height: 1.8;
		color: var(--text);
	}

	@media (min-width: 600px) {
		.statement {
			font-size: 1.5rem;
		}
	}

	.statement p {
		font-weight: 400; /* Normal weight */
		font-style: italic; /* Keep the italics from <em> */
		font-size: 1.8rem; /* Adjust size if needed */
		color: var(--text-subtle); /* Optional: Adjust color */
	}

	/* Style for the signatories counter */
	.signatories-counter {
		font-family: 'Arial', sans-serif;
		margin: 2rem 0;
		text-align: center;
		font-size: 1.2rem;
		/*border: 1px solid #ccc;
		padding: 1rem;
		background-color: #fff;
		border-radius: 8px;
		color: #444;*/
	}

	.signatories-counter p {
		margin: 0;
		font-weight: bold;
	}
	.signatories {
		display: grid;
		gap: 1rem;
	}

	/* Tally form container styling */
	.tally-form-container {
		margin: 2rem 0;
		padding: 1.5rem;
		background-color: #ffffff;
		border: 1px solid #e5e7eb;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	.tally-form-container iframe {
		border-radius: 4px;
	}

	button {
		margin-top: 1rem;
		padding: 0.5rem 1rem;
		background-color: var(--brand);
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
	}

	button:hover {
		background-color: var(--brand-dark);
	}

	.signatories-header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		margin-top: 2rem;
		margin-bottom: 1rem;
	}

	.expand-all {
		margin-left: 1rem;
		padding: 0.4rem 1rem;
		background-color: var(--brand);
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 1rem;
		float: right;
	}
	.expand-all:hover {
		background-color: var(--brand-dark);
	}
</style>
