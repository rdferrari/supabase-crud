<script lang="ts">
	let name: string = '';
	let isError = false;
	let loadingInsert = false;
	let message: string = '';
	export let supabase;
	export let user_id;
	export let centersData;

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
</script>

<h1>Centers CRUD</h1>
<h2>Insert New Center</h2>
<form on:submit|preventDefault={insertCenter}>
	<div>
		<label for="email">Name</label>
		<input id="name" class="inputField" type="text" placeholder="Center's name" bind:value={name} />
	</div>

	<div>
		<button class="bt-primary" type="submit" disabled={loadingInsert}>
			<span>{loadingInsert ? 'Loading' : 'New Center'}</span>
		</button>
	</div>
</form>
<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
