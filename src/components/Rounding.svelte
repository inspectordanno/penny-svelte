<script>
    import { scaleLinear } from "d3-scale";
    import sampleSize from "lodash/sampleSize";
    import round from "lodash/round";
    import meanBy from "lodash/meanBy";
    import { activeBlock } from "../store/store";
    import lombraData from "../../public/assets/lombra_sample.csv";
    import whaplesData from "../../public/assets/whaples_sample.csv";
    import Rect from "./Rect.svelte";

    //to-do: improve animation timing akin to https://penny.fyi
    //setup promise-timing for animation chaining? customize based on the activeBlock (in Rect.svelte)

    const colors = {
        red: "#e96a6a",
        lightRed: "#e4a19d",
        gray: "#d3d3d3",
        lightGreen: "#70866f",
        green: "#6aaa6a",
        lightBlue: "#cce7ff",
        lavender: "#e6e6fa",
    };

    const colorScale = scaleLinear()
        .domain([-0.02, -0.01, 0, 0.01, 0.02])
        .range([
            colors.red,
            colors.lightRed,
            colors.gray,
            colors.lightGreen,
            colors.green,
        ]);

    const roundToNickel = (price) => Math.round(price * 20) / 20;

    const numDatapoints = 100;
    const gridWidth = 800;
    const gridHeight = 500;
    const rectWidth = 65;
    const rectHeight = 35;
    const numCols = 10;
    const numRows = numDatapoints / numCols; //only works if there is no remainder; i.e. a whole number of rows and columns!

    const getGridPosition = (i) => {
        const currentRow = Math.floor(i / 10);

        const widthWhiteSpace = gridWidth - numCols * rectWidth;
        const heightWhiteSpace = gridHeight - numRows * rectHeight;

        const rowSpace = heightWhiteSpace / (numRows - 1);
        const colSpace = widthWhiteSpace / (numCols - 1);

        const x = (i % numCols) * (rectWidth + colSpace);
        const y = currentRow * (rectHeight + rowSpace);

        return { x, y };
    };

    const formatData = (sampledData, dataSource) => {
        const sourceColors = {
            lombra: colors.lightBlue,
            whaples: colors.lavender,
        };

        const formattedData = sampledData.map((item, i) => {
            const price =
                dataSource === "lombra" ? +item.price : +item.post_tax_amount; //this is the price that will be displayed to the screen
            const priceRounded = roundToNickel(price); //this is the price that is rounded to the nearest nickel
            const remainder = round(price - priceRounded, 2); //this calculates how much is gained or lost by rounding to the nearest nickel
            const category = item.category; //category of item (lombra only)
            const sourceColor = sourceColors[dataSource];
            const remainderColor = colorScale(remainder);
            const { x, y } = getGridPosition(i);

            return {
                x,
                y,
                id: `${dataSource}-${i}`,
                price,
                priceRounded,
                remainder,
                ...(category && { category }),
                sourceColor,
                remainderColor,
            };
        });

        return formattedData;
    };

    const colorData = (data, fillType, customFill) => {
        if (fillType) {
            return data.map((element) => ({
                ...element,
                fill: element[fillType],
            }));
        }
        return data.map((element) => ({ ...element, fill: customFill }));
    };

    const orderData = (data) => {
        const sorted = data.sort((a, b) => a.remainder - b.remainder);
        return sorted.map((element, i) => {
            const { x, y } = getGridPosition(i);
            return {
                ...element,
                x,
                y,
            };
        });
    };

    const getRemainderMean = (data) => {
        const mean = meanBy(data, "remainder");
        const meanRounded = round(mean, 2);

        return { mean, meanRounded };
    };

    const sampledLombraData = sampleSize(lombraData, numDatapoints);
    const sampledWhaplesData = sampleSize(whaplesData, numDatapoints);

    const formattedLombraData = formatData(sampledLombraData, "lombra");
    const formattedWhaplesData = formatData(sampledWhaplesData, "whaples");

    const meanColorLombra = colorScale(
        getRemainderMean(formattedLombraData).meanRounded
    );

    const meanColorWhaples = colorScale(
        getRemainderMean(formattedWhaplesData).meanRounded
    );

    // important / to-do: tween.set() and tween.update() return a promise - this suggests that animations can be chained?

    const setData = (dataStep) => {
        switch (dataStep) {
            // LOMBRA
            case "rounding-2":
                return colorData(formattedLombraData, "sourceColor");
            case "rounding-3":
                return colorData(formattedLombraData, "remainderColor");
            case "rounding-4":
                return orderData(
                    colorData(formattedLombraData, "remainderColor")
                );
            case "rounding-5":
                return orderData(
                    colorData(formattedLombraData, null, meanColorLombra)
                );
            // WHAPLES
            case "rounding-6":
                return colorData(formattedWhaplesData, "sourceColor");
            case "rounding-7":
                return colorData(formattedWhaplesData, "remainderColor");
            case "rounding-8":
                return orderData(
                    colorData(formattedWhaplesData, "remainderColor")
                );
            case "rounding-9":
                return orderData(
                    colorData(formattedWhaplesData, null, meanColorWhaples)
                );
            default:
                return colorData(formattedLombraData, "sourceColor");
        }
    };

    $: tweenedData = setData($activeBlock); //rerun every time step gets updated
</script>

<style>
</style>

{#if $activeBlock === 'rounding-1' || $activeBlock === ''}
    <img
        width="100%"
        src="./assets/rounding-overview.png"
        alt="rounding-overview" />
{:else}
    <svg width={`${gridWidth}px`} height={`${gridHeight}px`}>
        {#each tweenedData as datum (datum.id)}
            <Rect
                x={datum.x}
                y={datum.y}
                width={rectWidth}
                height={rectHeight}
                fill={datum.fill} />
        {/each}
    </svg>
{/if}
