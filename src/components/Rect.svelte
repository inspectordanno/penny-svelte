<script>
    import { tweened } from "svelte/motion";
    import { interpolateRgb } from "d3-interpolate";
    import { easeCubic } from "d3-ease";

    export let x;
    export let y;
    export let width;
    export let height;
    export let fill;

    const tweenedX = tweened(x, { easing: easeCubic });
    const tweenedY = tweened(y, { easing: easeCubic });
    const tweenedFill = tweened(fill, {
        interpolate: interpolateRgb,
    });

    const animate = async () => {
        tweenedFill.set(fill);
        await tweenedX.set(x);
        await tweenedY.set(y);
    };

    $: x, y, fill, animate();
</script>

<rect x={$tweenedX} y={$tweenedY} {width} {height} fill={$tweenedFill} />
