<script lang="ts">
	import '../app.css';
	import Navbar from '../component/Navbar.svelte';

	let { children } = $props();
</script>

<Navbar />
{@render children()}
