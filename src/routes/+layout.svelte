<script>
	import '../app.pcss';
	import { browser } from '$app/environment';
	import { page } from '$app/stores';
	import { webVitals } from '$lib/vitals';
	import './styles.css';

	/** @type {import('./$types').LayoutServerData} */
	export let data;

	$: if (browser && data?.analyticsId) {
		webVitals({
			path: $page.url.pathname,
			params: $page.params,
			analyticsId: data.analyticsId
		});
	}
</script>

<div class="app">
	<slot />
</div>

<style>
	.app {
		flex: 1;
		display: flex;
		padding: 36px;
		gap: 24px;
		width: 100%;
		margin: 0 auto;
		box-sizing: border-box;
	}
</style>
