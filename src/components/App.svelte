<script>
	import Header from "./Header.svelte";
	import Scroll from "./Scroll.svelte";
	import text from "../util/text.json";
</script>

<style>
</style>

<div>
	<Header />
	<Scroll text={text.rounding} />
</div>
