<script lang="ts">
	import '../../app.css';
	
	let { children } = $props();
</script>

<div class="flex p-2.5 bg-slate-700">
	<h1 class="text-2xl md:text-3xl pl-2.5">Blog: Reader Site</h1>
	<nav class="flex p-2.5 w-2/4 ml-auto">
		<a href="/log-in" class="grow text-sm md:text-lg py-1 hover:text-slate-200 text-center">Log In</a>
		<a href="/sign-up" class="grow text-sm md:text-lg py-1 hover:text-slate-200 text-center">Create an Account</a>
	</nav>
</div>

{@render children()}