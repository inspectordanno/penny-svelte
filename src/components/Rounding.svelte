<script>
    import { scaleLinear } from "d3-scale";
    import { onMount } from "svelte";
    import { tweened } from "svelte/motion";
    import sampleSize from "lodash/sampleSize";
    import round from "lodash/round";
    import meanBy from "lodash/meanBy";
    import { activeBlock, graphicContainerWidth } from "../store/store";
    import lombraData from "../../public/assets/lombra_sample.csv";
    import whaplesData from "../../public/assets/whaples_sample.csv";

    const numDatapoints = 100;

    let sampledLombraData;
    let sampledWhaplesData;

    onMount(() => {
        sampledLombraData = sampleSize(lombraData, numDatapoints);
        sampledWhaplesData = sampleSize(whaplesData, numDatapoints);
    });

    const colors = {
        red: "#a34a4a",
        lightRed: "#a0716e",
        gray: "#949494",
        lightGreen: "#70866f",
        green: "4a774a",
        lightBlue: "#8fa2b3",
        lavender: "#a1a1af",
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

    const gridWidth = 800;
    const gridHeight = 500;
    const rectWidth = 65;
    const rectHeight = 35;
    const numCols = 10;

    const getGridPosition = (i) => {
        const numRows = arr.length / numCols; //only works if there is no remainder; i.e. a whole number of rows and columns!

        const widthWhiteSpace = gridWidth - numCols * rectWidth;
        const heightWhiteSpace = gridHeight - numRows * rectHeight;

        const rowSpace = widthWhiteSpace / (numCols - 1);
        const colSpace = heightWhiteSpace / (numRows - 1);

        const x = (i % numCols) * (rectWidth * rowSpace);
        const y = (i % numRows) * (rectHeight * colSpace);

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
            const remainderColor = colorScale(item.remainder);
            const { x, y } = getGridPosition(i);

            return {
                price,
                priceRounded,
                remainder,
                ...(category && { category }),
                sourceColor,
                remainderColor,
                x,
                y,
            };
        });

        return formattedData;
    };

    const orderData = (data) => {
        const sorted = data.sort((a, b) => a.remainder - b.remainder);
        return sorted.map(element, (i) => {
            const { x, y } = gridGridPosition(i);
            return {
                ...element,
                x,
                y,
            };
        });
    };

    const setColor = (element, colorType, customColor) => {
        const color = colorType ? element[colorType] : customColor;
        return {
            ...element,
            color,
        };
    };

    const getRemainderMean = (data) => {
        const mean = meanBy(data, "remainder");
        const meanRounded = round(mean, 2);

        return { mean, meanRounded };
    };

    const formattedLombraData = formatData(sampledLombraData, "lombra");
    const formattedWhaplesData = formatData(sampledWhaplesData, "whaples");

    //use tweened data in html
    let tweenedData = tweened(formattedLombraData); // initial state

    const setData = (dataStep) => {
        switch (dataStep) {
            // LOMBRA
            case "rounding-2":
                data.update((item) => setColor(item, "sourceColor"));
                break;
            case "rounding-3":
                data.update((item) => setColor(item, "remainderColor"));
                break;
            case "rounding-4":
                data.set(
                    orderData(formattedLombraData).map((item) =>
                        setColor(item, "remainderColor")
                    )
                );
                break;
            case "rounding-5":
                data.update((item) => {
                    setColor(
                        item,
                        null,
                        colorScale(
                            getRemainderMean(formattedLombraData).meanRounded
                        )
                    );
                });
                break;
            case "rounding-6":
            // WHAPLES
            case "rounding-7":
                data.set(
                    formattedWhaplesData.map((transaction) =>
                        setColor(transaction, "sourceColor")
                    )
                );
            case "rounding-8":
                data.update((item) => setColor(item, "remainderColor"));
                break;
            case "rounding-9":
                data.set(
                    orderData(formattedWhaplesData).map((item) =>
                        setColor(item, "remainderColor")
                    )
                );
                break;
            case "rounding-10":
                data.update((item) => {
                    setColor(
                        item,
                        null,
                        colorScale(
                            getRemainderMean(formattedWhaplesData).meanRounded
                        )
                    );
                });
                break;
        }
    };
</script>

<style>
</style>

{#if $activeBlock === 'rounding-1'}
    <img
        width={'100%'}
        src="./assets/rounding-overview.png"
        alt="rounding-overview" />
{:else}
    <svg width={`${$graphicContainerWidth}px`}>
        <rect />
        <!-- tweened data each block -->
    </svg>
{/if}
