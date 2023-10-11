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

<div class="app">
	<!-- <Header /> -->

	{#if !session}
		<Auth />
	{:else}
		<button type="button" class="button block" on:click={() => supabase.auth.signOut()}>
			Sign Out
		</button>
		<main>
			<slot />
		</main>
	{/if}

	<footer>
		<p>visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to learn SvelteKit</p>
	</footer>
</div>

<style>
	.app {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		padding: 1rem;
		width: 100%;
		max-width: 64rem;
		margin: 0 auto;
		box-sizing: border-box;
	}

	footer {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		padding: 12px;
	}

	footer a {
		font-weight: bold;
	}

	@media (min-width: 480px) {
		footer {
			padding: 12px 0;
		}
	}
</style>
