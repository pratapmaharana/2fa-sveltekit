<script>
	import '../app.css';

	let { children } = $props();
</script>

<div class="app">
	<main class="bg-sky-100 min-h-screen p-10 flex items-center justify-center">
		{@render children()}
	</main>
</div>
