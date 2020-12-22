<script>
	import { onMount } from "svelte";
	import scrollama from "scrollama";
	import Header from "./Header.svelte";
	import Scroll from "./Scroll.svelte";
	import Rounding from "./Rounding.svelte";
	import text from "../util/text.json";

	let activeBlock = "";

	let graphicContainerWidth;
	let graphicContainerHeight;

	console.log(graphicContainerWidth);

	onMount(() => {
		const scroller = scrollama();

		scroller
			.setup({
				step: ".block",
				offset: 0.75,
			})
			.onStepEnter(({ element }) => {
				activeBlock = element.getAttribute("data-step");
			});

		window.addEventListener("resize", scroller.resize);
	});
</script>

<style>
</style>

<div>
	<Header />
	<Scroll
		text={text.rounding}
		topic={'rounding'}
		{activeBlock}
		{graphicContainerWidth}
		{graphicContainerHeight}>
		<Rounding {activeBlock} />
	</Scroll>
</div>
