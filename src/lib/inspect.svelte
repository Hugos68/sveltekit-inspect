<script lang="ts">
	import './preflight.css';
	import '@fontsource/roboto';
	import {
		useFloating,
		useRole,
		useClick,
		useDismiss,
		useInteractions,
		autoUpdate,
		offset,
		FloatingArrow,
		arrow
	} from '@skeletonlabs/floating-ui-svelte';
	import SvelteLogo from './svelte-logo.svelte';
	import { page } from '$app/stores';
	import { fly } from 'svelte/transition';
	import { cubicOut } from 'svelte/easing';

	interface Props {}

	let {}: Props = $props();

	let open = $state(false);
	let locked = $state(false);
	let arrowEl: HTMLElement | null = $state(null);

	const popover = useFloating({
		whileElementsMounted: autoUpdate,
		get open() {
			return open;
		},
		onOpenChange(v) {
			open = v;
		},
		transform: false,
		placement: 'top',
		get middleware() {
			return [offset(10), arrowEl && arrow({ element: arrowEl })];
		}
	});

	const role = useRole(popover.context);
	const click = useClick(popover.context);
	const dismiss = useDismiss(popover.context);

	const interactions = useInteractions([role, click, dismiss]);
</script>

<button
	data-open={open}
	class="reference"
	bind:this={popover.elements.reference}
	{...interactions.getReferenceProps()}
>
	<SvelteLogo height={30} />
	Inspect
</button>

{#if open}
	<div
		transition:fly={{ y: 50, duration: 200, easing: cubicOut }}
		class="floating"
		bind:this={popover.elements.floating}
		style={popover.floatingStyles}
		{...interactions.getFloatingProps()}
	>
		<label>
			Lock
			<input type="checkbox" bind:checked={locked} />
		</label>
		<pre>{JSON.stringify($page.data, null, 2)}</pre>
		<pre>{JSON.stringify($page.form, null, 2)}</pre>
		<FloatingArrow context={popover.context} bind:ref={arrowEl} fill="#1A1A1A"></FloatingArrow>
	</div>
{/if}

<style>
	.reference {
		font-family: 'Roboto', sans-serif;

		position: fixed;
		left: 50%;
		translate: -50% 50%;
		bottom: 0;

		display: flex;
		align-items: center;
		gap: 0.75rem;

		background-color: #1a1a1a;
		color: #fff;
		font-weight: 600;
		font-size: 1.25rem;

		border-radius: 1rem;
		padding: 1rem;
		margin-bottom: 0.5rem;

		transition:
			background 100ms ease-in-out,
			translate 250ms ease-in-out;

		z-index: 1;
	}

	.reference:hover {
		background-color: #383838;
	}

	.reference:hover,
	.reference[data-open='true'] {
		translate: -50% 0%;
	}

	.floating {
		font-family: 'Roboto', sans-serif;

		background-color: #1a1a1a;
		color: #fff;

		border-radius: 1rem;
		padding: 1rem;

		z-index: 0;
	}
</style>
