<script>
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<title>Star Wars Weather</title>
	<meta name="description" content="A weather app which compares the conditions to a planet from Star Wars.">
</svelte:head>
{@render children()}
