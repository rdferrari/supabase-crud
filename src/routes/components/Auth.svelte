<script lang="ts">
	import { supabase } from '../supabaseClient';

	let loading = false;
	let email = '';
	let password = '';
	let isSigningUp = false;
	let message = '';
	let isError = false;
	export let handleIsForgotPassword = () => {};

	const handleSignIn = async () => {
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

	const handleSignUp = async () => {
		try {
			loading = true;
			message = '';
			isError = false;
			const { data, error } = await supabase.auth.signUp({ email, password });
			if (error) throw error;

			console.log('Signed up: ', data);
			message = 'Check your email!';
			// alert('Check your email for login link!');
		} catch (error) {
			if (error instanceof Error) {
				isError = true;
				message = error.message;
			}
		} finally {
			loading = false;
		}
	};

	const handleIsSignUp = () => {
		isSigningUp === true ? (isSigningUp = false) : (isSigningUp = true);
		message = '';
		isError = false;
	};
</script>

<div class="flex-container-center">
	<div class="content-container">
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
		<p class={isError ? 'error-message' : 'success-message'}>{message}</p>
		<div>
			<p>
				{isSigningUp ? 'Have an accound ' : "Don't have an account?"}
				<button class="bt-text" on:click={handleIsSignUp}
					>{isSigningUp ? 'Sign In Now' : 'Sign Up Now'}</button
				>
				or
				<button on:click={handleIsForgotPassword} class="bt-text">Forgot Password?</button>
			</p>
		</div>
	</div>
</div>
