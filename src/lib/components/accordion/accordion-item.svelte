<script lang="ts">
	import { slide } from 'svelte/transition';
	import { getAccordionOptions } from './context';

	export let open = false;

	const componentId = crypto.randomUUID();
	const { activeComponentId, collapse } = getAccordionOptions();

	function setActive() {
		$activeComponentId = componentId;
	}

	function toggleOpen() {
		open = !open;
	}

	function handleClick() {
		collapse ? setActive() : toggleOpen();
	}

	$: open && collapse && setActive();
	$: isActive = $activeComponentId === componentId;
	$: isOpen = collapse ? isActive : open;
</script>

<div class="accordion-item">
	<button
		on:click={handleClick}
		class="accordion-toggle"
		aria-expanded={isOpen}
		aria-controls="accordion-{componentId}"
	>
		<div class="accordion-title">
			<slot name="title" />
		</div>
		<div class="accordion-caret" class:open={isOpen}>👉</div>
	</button>
	{#if isOpen}
		<div
			class="accordion-content"
			transition:slide|local
			role="region"
			aria-hidden={!isOpen}
			aria-labelledby="accordion-{componentId}"
		>
			<slot name="content" />
		</div>
	{/if}
</div>

<style>
	.accordion-toggle {
		width: 100%;
		display: flex;
		justify-content: space-between;
		padding: var(--accordion-padding, 1rem);
		color: var(--accordion-color, inherit);
		font: inherit;
		font-weight: 600;
		border: none;
		background: none;
		cursor: pointer;
		border-radius: var(--accordion-radius, 4px);
		transition: background-color 0.1s ease;
	}

	.accordion-toggle:hover {
		background-color: var(--accordion-hover, hsl(220 20% 20%));
	}

	.accordion-caret {
		transition: rotate 0.3s ease;
	}

	.accordion-content {
		padding: var(--accordion-content-padding, 1rem);
	}

	.open {
		rotate: 90deg;
	}
</style>
