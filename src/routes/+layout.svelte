<script>
	import '../app.css';

	let { children } = $props();

	let innerHeight = 695;
</script>

<!-- <div class="app">
	<main class="bg-sky-100 min-h-screen p-10 flex items-center justify-center">
		{@render children()}
	</main>
</div> -->

<div class="app">
	<div class="container mx-auto h-screen overflow-hidden">
		<header class="h-[60px] bg-orange-500 p-5 text-center flex items-center justify-center">
			Header
		</header>
		<div class="flex text-center" style="height: {innerHeight - 160}px;">
			<nav class="w-[100px] h-full bg-orange-400">Navigation</nav>
			<main class="w-full bg-orange-200">{@render children()}</main>
			<aside class="w-[100px] bg-orange-400">Sidebar</aside>
		</div>
		<footer class="h-[100px] bg-gray-400 pb-20 text-center">Footer</footer>
	</div>
</div>
