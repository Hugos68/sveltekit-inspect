<script>
	import { dev } from '$app/environment';
	import Inspect from './inspect.svelte';
</script>

{#if dev}
	<Inspect></Inspect>
{/if}
