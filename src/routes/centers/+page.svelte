<script lang="ts">
	import { onMount } from 'svelte';
	import { supabase } from '../supabaseClient';
	import type { T } from 'vitest/dist/types-198fd1d9';
	import sessionAuth from '../../lib/session';

	let centersData: Array<T> | null;
	let loadingList = false;
	let startIndex = 0;
	let endIndex = 9;
	let maxListItems = endIndex + 1;
	console.log(startIndex, endIndex);
	let loadingInsert = false;
	let isError = false;

	let name: string = '';
	let message: string = '';
	let user_id: string;

	let session;

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

	const insertCenter = async () => {
		try {
			loadingInsert = true;

			const { data, error } = await supabase.from('centers').insert([{ user_id, name }]).select();

			if (error) throw error;

			loadingInsert = false;
			isError = false;
			console.log(data);
			message = 'Center created';

			centersData = [data[0], ...centersData];
			name = '';
		} catch (error) {
			loadingInsert = false;
			console.log(error);
			isError = true;
			if (error.code === '23514') {
				message = "Center's name must be greater than 3 char!";
			}
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

	onMount(() => {
		readCenters();
	});
</script>

<svelte:head>
	<title>Centers</title>
	<meta name="description" content="About this app" />
</svelte:head>

<div class="flex-container">
	<div class="content-container">
		<h1>Centers CRUD</h1>
		<h2>Insert New Center</h2>
		<form on:submit|preventDefault={insertCenter}>
			<div>
				<label for="email">Name</label>
				<input
					id="name"
					class="inputField"
					type="text"
					placeholder="Center's name"
					bind:value={name}
				/>
			</div>

			<div>
				<button class="bt-primary" type="submit" disabled={loadingInsert}>
					<span>{loadingInsert ? 'Loading' : 'New Center'}</span>
				</button>
			</div>
		</form>
		<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
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
	</div>
</div>
