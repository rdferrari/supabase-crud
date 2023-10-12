<script lang="ts">
	import { supabase } from '../supabaseClient';

	let loading = false;
	let password = '';
	let message = '';
	let isError = false;
	export let handleCancelResetPassword = () => {};

	const handleNewPassword = async () => {
		try {
			loading = true;
			message = '';
			isError = false;
			const { data, error } = await supabase.auth.updateUser({
				password: password
			});
			if (error) throw error;
			message = 'Password updated';

			console.log(data);
		} catch (error) {
			if (error instanceof Error) {
				isError = true;
				message = error.message;
			}
		} finally {
			loading = false;
		}
	};
</script>

<div class="flex-container-center">
	<div class="content-container">
		<h1>New Password</h1>
		<p>Type in your new password</p>
		<form on:submit|preventDefault={handleNewPassword}>
			<div>
				<label for="password">New Password</label>
				<input
					id="password"
					class="inputField"
					type="password"
					placeholder="New Password"
					bind:value={password}
				/>
			</div>

			<div>
				<button class="bt-primary" type="submit" disabled={loading}>
					<span>{loading ? 'Loading' : 'Reset Password'}</span>
				</button>
			</div>
		</form>
		<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
		<div>
			<button on:click={handleCancelResetPassword} class="bt-text">Cancel</button>
		</div>
	</div>
</div>
