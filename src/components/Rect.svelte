<script>
    import { tweened } from "svelte/motion";
    import { interpolateRgb } from "d3-interpolate";

    export let x;
    export let y;
    export let width;
    export let height;
    export let fill;

    const tweenedX = tweened(x);
    const tweenedY = tweened(y);
    const tweenedFill = tweened(fill, {
        interpolate: interpolateRgb,
    });

    $: $tweenedX = x;
    $: $tweenedX = y;
    $: $tweenedFill = fill;
</script>

<rect {x} {y} {width} {height} fill={$tweenedFill} />
