<script lang="ts">
	import { supabase } from '../supabaseClient';

	let loading = false;
	let email = '';
	let password = '';
	let isSigningUp = false;

	const handleSignIn = async () => {
		try {
			loading = true;
			const { error } = await supabase.auth.signInWithPassword({ email, password });
			if (error) throw error;
			// alert('Check your email for login link!');
		} catch (error) {
			if (error instanceof Error) {
				alert(error.message);
			}
		} finally {
			loading = false;
		}
	};

	const handleSignUp = async () => {
		try {
			loading = true;
			const { error } = await supabase.auth.signUp({ email, password });
			if (error) throw error;
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

<div>
	<div>
		<h1>{isSigningUp ? 'Sign Up' : 'Sign In'}</h1>
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
				<button type="submit" disabled={loading}>
					<span>{loading ? 'Loading' : isSigningUp ? 'Sign Up' : 'Sign In'}</span>
				</button>
			</div>
		</form>
		<p>{isSigningUp ? 'If you already have an accound ' : 'Create a '}</p>
		<button on:click={handleIsSignUp}>{isSigningUp ? 'Sign In' : 'new Account'}</button>
	</div>
</div>
