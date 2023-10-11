<script lang="ts">
	import './styles.css';
	import { onMount } from 'svelte';
	import { supabase } from './supabaseClient';
	import type { AuthSession } from '@supabase/supabase-js';
	import Auth from './components/Auth.svelte';

	let session: AuthSession;

	onMount(() => {
		supabase.auth.getSession().then(({ data }) => {
			session = data.session;
		});

		supabase.auth.onAuthStateChange((_event, _session) => {
			session = _session;
			console.log(session);
		});
	});
</script>

<div>
	{#if !session}
		<Auth />
	{:else}
		<button type="button" class="button block" on:click={() => supabase.auth.signOut()}>
			Sign Out
		</button>
		<slot />
	{/if}
</div>
