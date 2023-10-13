<script lang="ts">
	import { onMount } from 'svelte';
	import { supabase } from '../supabaseClient';
	import type { T } from 'vitest/dist/types-198fd1d9';
	import sessionAuth from '../../lib/session';
	import Form from './Form.svelte';
	import List from './List.svelte';

	let centersData: Array<T> | null;
	let loadingList = false;
	let startIndex = 0;
	let endIndex = 9;
	let maxListItems = endIndex + 1;
	console.log(startIndex, endIndex);

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
		<Form {supabase} {user_id} {centersData} />
		<List {centersData} {handlePrevious} {handleNext} {startIndex} {maxListItems} />
	</div>
</div>
