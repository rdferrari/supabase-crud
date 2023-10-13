<script lang="ts">
	import type { T } from 'vitest/dist/types-198fd1d9';

	export let startIndex;
	export let maxListItems;
	export let centersData: Array<T> | null;
	export let handlePrevious = () => {};
	export let handleNext = () => {};
</script>

<h2>Centers List</h2>
{#if centersData?.length === 0}
	<p>List is empty</p>
{:else if centersData}
	{#each centersData as center}
		<p>{center.name}</p>
	{/each}
{/if}

{#if startIndex !== 0}
	<button class="bt-text" on:click={handlePrevious}>previous</button>
{/if}
{#if centersData?.length === maxListItems}
	<button class="bt-text" on:click={handleNext}>next</button>
{/if}
