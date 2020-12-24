<script>
	import { onMount } from "svelte";
	import scrollama from "scrollama";
	import Header from "./Header.svelte";
	import Scroll from "./Scroll.svelte";
	import Rounding from "./Rounding.svelte";
	import { activeBlock, windowHeight } from "../store/store";
	import text from "../util/text.json";

	onMount(() => {
		const scroller = scrollama();

		scroller
			.setup({
				step: ".block",
				offset: 0.75,
			})
			.onStepEnter(({ element }) => {
				const dataAttribute = element.getAttribute("data-step");
				activeBlock.set(dataAttribute);
			});

		window.addEventListener("resize", scroller.resize);
	});
</script>

<style>
</style>

<svelte:window bind:innerHeight={$windowHeight} />

<div>
	<Header />
	<Scroll text={text.rounding} topic={'rounding'}>
		<Rounding />
	</Scroll>
</div>
