<script>
	import { page } from '$app/stores';
	import logo from '$lib/images/svelte-logo.svg';
	import github from '$lib/images/github.svg';
</script>

<header>
	<!-- <div class="corner">
		<a href="https://kit.svelte.dev">
			<img src={logo} alt="SvelteKit" />
		</a>
	</div> -->

	<a href="/">Home</a>
	<a href="/about">About</a>
	<a href="/sverdle">Sverdle</a>
</header>

<style>
	header {
		display: flex;
		justify-content: space-between;
	}
</style>
