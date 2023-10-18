<script lang="ts">
	import { supabase } from '../supabaseClient';
	import type { T } from 'vitest/dist/types-198fd1d9';

	export let startIndex;
	export let maxListItems;
	export let centersData: Array<T> | null;
	export let handlePrevious = () => {};
	export let handleNext = () => {};
	export let readCenters = () => {};
	export let handleDetail = () => {};
	export let searchInput;
	export let selectedDetailId;

	console.log(searchInput);

	let loading = false;
	let isError = false;
	let message = '';

	const updateCenter = async (id: string) => {
		try {
			loading = true;

			const { data, error } = await supabase
				.from('centers')
				.update({ is_active: 'False' })
				.eq('id', id)
				.select();

			if (error) throw error;

			loading = false;
			isError = false;
			console.log(data);
			const itemDeletedId = data[0].id;
			message = 'Center removed';

			const newCentersData = centersData?.filter((center) => {
				return center.id !== itemDeletedId;
			});

			centersData = newCentersData;
		} catch (error) {
			loading = false;
			console.log(error);
			isError = true;
			// if (error.code === '23514') {
			// 	message = "Center's name must be greater than 3 char!";
			// }
		}
	};
</script>

<h2>Centers List</h2>
{#if centersData?.length === 0}
	<p>List is empty</p>
{:else if centersData}
	<input
		type="text"
		id="search-field"
		placeholder="Enter Search Term"
		autocomplete="off"
		bind:value={searchInput}
		on:input
	/>
	<div class="list-container">
		{#each centersData as center}
			<div class="item-container">
				<p>{center.name}</p>
				<div class="bt-container">
					<button class="bt-text" on:click={handleDetail(center.id)}>details</button>
					<button class="bt-text" on:click={updateCenter(center.id)}>delete</button>
				</div>
			</div>
		{/each}
	</div>
{/if}

{#if startIndex !== 0}
	<button class="bt-text" on:click={handlePrevious}>previous</button>
{/if}
{#if centersData?.length === maxListItems}
	<button class="bt-text" on:click={handleNext}>next</button>
{/if}

<style>
	.list-container {
		margin-bottom: 60px;
	}
	.item-container {
		display: flex;
		justify-content: space-between;
		border-bottom: 1px solid #939393;
	}
	.bt-container {
		margin-top: 20px;
	}
</style>
