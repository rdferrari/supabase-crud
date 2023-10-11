<script lang="ts">
	import { supabase } from '../supabaseClient';

	let loading = false;
	let email = '';
	let password = '';
	let isSigningUp = false;
	let message = '';

	const handleSignIn = async () => {
		try {
			loading = true;
			message = '';
			const { data, error } = await supabase.auth.signInWithPassword({ email, password });
			if (error) throw error;

			console.log('Signed in: ', data);
		} catch (error) {
			if (error instanceof Error) {
				message = error.message;
			}
		} finally {
			loading = false;
		}
	};

	const handleSignUp = async () => {
		try {
			loading = true;
			message = '';
			const { data, error } = await supabase.auth.signUp({ email, password });
			if (error) throw error;

			console.log('Signed up: ', data);
			// alert('Check your email for login link!');
		} catch (error) {
			if (error instanceof Error) {
				alert(error.message);
			}
		} finally {
			loading = false;
		}
	};

	const handleIsSignUp = () =>
		isSigningUp === true ? (isSigningUp = false) : (isSigningUp = true);
</script>

<div class="flex-container-center">
	<div class="auth-container">
		<h1>{isSigningUp ? 'Get started' : 'Welcome back'}</h1>
		<p>{isSigningUp ? 'Create a new account' : 'Sign In to your account'}</p>
		<form on:submit|preventDefault={isSigningUp ? handleSignUp : handleSignIn}>
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
				<label for="password">Password</label>
				<input
					id="password"
					class="inputField"
					type="password"
					placeholder="Your password"
					bind:value={password}
				/>
			</div>
			<div>
				<button class="bt-primary" type="submit" disabled={loading}>
					<span>{loading ? 'Loading' : isSigningUp ? 'Sign Up' : 'Sign In'}</span>
				</button>
			</div>
		</form>
		<p class="error-message">{message}</p>
		<div>
			<p>
				{isSigningUp ? 'Have an accound ' : "Don't have an account?"}
				<button class="bt-text" on:click={handleIsSignUp}
					>{isSigningUp ? 'Sign In Now' : 'Sign Up Now'}</button
				>
			</p>
		</div>
	</div>
</div>

<style>
	.auth-container {
		width: 100%;
		max-width: 400px;
		padding: 0 20px;
	}

	@media (min-width: 720px) {
		.auth-container {
		}
	}
</style>
