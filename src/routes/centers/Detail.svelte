<script lang="ts">
	import { onMount } from 'svelte';

	let name: string = '';
	let isError = false;
	let loading = false;
	let message: string = '';
	let center;
	export let supabase;
	export let user_id;
	export let selectedDetailId: string;
	export let centersData;

	const readCenter = async () => {
		try {
			let { data: centers, error } = await supabase
				.from('centers')
				.select('*')
				.eq('id', selectedDetailId);

			if (error) throw error;

			center = centers[0];
			name = center.name;
			console.log(center);
		} catch (error) {
			console.log(error);
		}
	};

	const updateCenter = async () => {
		try {
			loading = true;

			const { data, error } = await supabase
				.from('centers')
				.update({ name: name })
				.eq('id', selectedDetailId)
				.select();

			if (error) throw error;

			loading = false;
			isError = false;
			console.log(data);
			message = 'Center created';

			centersData.map((center) => {
				console.log(center.id, data[0].id);
				if (center.id === data[0].id) {
					center.name = data[0].name;
				}
			});
		} catch (error) {
			loading = false;
			console.log(error);
			isError = true;
			if (error.code === '23514') {
				message = "Center's name must be greater than 3 char!";
			}
		}
	};

	onMount(() => {
		readCenter();
	});
</script>

<h1>Center Detail</h1>
<h2>Update Center</h2>

{#if center}
	<p>id: {selectedDetailId} = {center.id}</p>
	<form on:submit|preventDefault={updateCenter}>
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
			<button class="bt-primary" type="submit" disabled={loading}>
				<span>{loading ? 'Loading' : 'Update Center'}</span>
			</button>
		</div>
	</form>
	<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
{/if}
