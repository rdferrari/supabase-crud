<script lang="ts">
	import { supabase } from '../supabaseClient';

	let loading = false;
	let email = '';
	let message = '';
	let isError = false;
	export let handleIsForgotPassword = () => {};

	const handleSendResetEmail = async () => {
		try {
			loading = true;
			message = '';
			isError = false;
			const { data, error } = await supabase.auth.signInWithPassword({ email, password });
			if (error) throw error;

			console.log('Signed in: ', data);
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
		<h1>Reset Your Password</h1>
		<p>Type in your email and we'll send you a link to reset your password</p>
		<form on:submit|preventDefault={handleSendResetEmail}>
			<div>
				<label for="email">Email</label>
				<input
					id="email"
					class="inputField"
					type="email"
					placeholder="Your email"
					bind:value={email}
				/>
			</div>

			<div>
				<button class="bt-primary" type="submit" disabled={loading}>
					<span>{loading ? 'Loading' : 'Send Reset Email'}</span>
				</button>
			</div>
		</form>
		<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
		<div>
			<p>
				Have an accound
				<button on:click={handleIsForgotPassword} class="bt-text">Sign In</button>
			</p>
		</div>
	</div>
</div>
