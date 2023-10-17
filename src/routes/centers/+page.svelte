<script lang="ts">
	import { onMount } from 'svelte';
	import { supabase } from '../supabaseClient';
	import type { T } from 'vitest/dist/types-198fd1d9';
	import sessionAuth from '../../lib/session';
	import Form from './Form.svelte';
	import List from './List.svelte';
	import Detail from './Detail.svelte';

	let centersData: Array<T> | null;
	let loadingList = false;
	let startIndex = 0;
	let endIndex = 9;
	let maxListItems = endIndex + 1;
	let searchInput: string = '';
	let user_id: string;
	let session;
	let selectedDetailId = '';
	console.log(selectedDetailId);

	sessionAuth.subscribe((value) => {
		session = value;
		user_id = session.user.id;
		console.log(user_id);
	});

	const readCenters = async () => {
		try {
			loadingList = true;

			let { data: centers, error } = await supabase
				.from('centers')
				.select('*')
				.order('inserted_at', { ascending: false })
				.eq('is_active', 'TRUE')
				.like('name', `%${searchInput}%`)
				.range(startIndex, endIndex);

			if (error) throw error;

			centersData = centers;
			loadingList = false;
			console.log(centersData);
		} catch (error) {
			console.log(error);
			loadingList = false;
		}
	};

	const handleNext = () => {
		startIndex = endIndex + 1;
		endIndex = endIndex + maxListItems;
		readCenters();
		console.log(startIndex, endIndex);
	};

	const handlePrevious = () => {
		endIndex = startIndex - 1;
		startIndex = startIndex - maxListItems;
		readCenters();
		console.log(startIndex, endIndex);
	};

	const handleDetail = async (id: string) => {
		try {
			selectedDetailId = id;
			console.log(selectedDetailId);
		} catch (error) {
			console.log(error);
		}
	};

	onMount(() => {
		readCenters();
	});
</script>

<svelte:head>
	<title>Centers</title>
	<meta name="description" content="Centers CRUD page" />
</svelte:head>

<div class="flex-container">
	<div class="content-container">
		{#if selectedDetailId !== ''}
			<button class="bt-text" on:click={() => handleDetail('')}>back</button>
			<Detail {supabase} {user_id} {selectedDetailId} {centersData} />
		{:else if selectedDetailId === ''}
			<Form {supabase} {user_id} {centersData} />
			<List
				{handlePrevious}
				{handleNext}
				{handleDetail}
				{centersData}
				{startIndex}
				{maxListItems}
				{selectedDetailId}
				bind:searchInput
				on:input={readCenters}
			/>
		{/if}
	</div>
</div>
