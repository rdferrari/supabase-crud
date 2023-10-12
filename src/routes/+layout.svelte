<script lang="ts">
	import './styles.css';
	import { onMount } from 'svelte';
	import { supabase } from './supabaseClient';
	import type { AuthSession } from '@supabase/supabase-js';
	import Auth from './components/Auth.svelte';
	import ForgotPassword from './components/ForgotPassword.svelte';
	import ResetPassword from './components/ResetPassword.svelte';

	let session: AuthSession | null;
	let event: string | undefined;
	let email: string | undefined;
	let id: string | undefined;
	let isForgotPassword = false;

	onMount(() => {
		supabase.auth.getSession().then(({ data }) => {
			session = data.session;
			email = session.user.email;
			id = session.user.id;
			console.log(email, id);
		});

		supabase.auth.onAuthStateChange((_event, _session) => {
			session = _session;
			event = _event;
			console.log(session, event);
			email = session.user.email;
			id = session.user.id;
			console.log(email, id);
		});
	});

	const handleIsForgotPassword = () =>
		isForgotPassword === true ? (isForgotPassword = false) : (isForgotPassword = true);
</script>

<div>
	{#if !session}
		{#if isForgotPassword === false}
			<Auth {handleIsForgotPassword} />
		{:else}
			<ForgotPassword {handleIsForgotPassword} />
		{/if}
	{:else}
		<div class="signout-container">
			<button type="button" class="bt-primary" on:click={() => supabase.auth.signOut()}>
				Sign Out
			</button>
		</div>

		{#if event === 'PASSWORD_RECOVERY'}
			<ResetPassword />
		{:else}
			<slot />
		{/if}
	{/if}
</div>

<style>
	.signout-container {
		position: fixed;
		right: 20px;
		top: 20px;
		width: 100px;
	}
</style>
