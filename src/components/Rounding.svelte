<script>
    import { scaleLinear } from "d3-scale";
    import sampleSize from "lodash/sampleSize";
    import round from "lodash/round";
    import orderBy from "lodash/orderBy";
    import meanBy from "lodash/meanBy";
    import { activeBlock, graphicContainerWidth } from "../store/store";
    import lombraData from "../../public/assets/lombra_sample.csv";
    import whaplesData from "../../public/assets/whaples_sample.csv";

    const colors = {
        red: "#a34a4a",
        lightRed: "#a0716e",
        gray: "#949494",
        lightGreen: "#70866f",
        green: "4a774a",
        lightblue: "#8fa2b3",
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

    const getGridPositions = (numElements, i) => {
        const numRows = numElements / numCols; //only works if there is no remainder; i.e. a whole number of rows and columns!

        const widthWhiteSpace = gridWidth - numCols * rectWidth;
        const heightWhiteSpace = gridHeight - numRows * rectHeight;

        const rowSpace = widthWhiteSpace / (numCols - 1);
        const colSpace = heightWhiteSpace / (numRows - 1);

        const x = (i % numCols) * (rectWidth * rowSpace);
        const y = (i % rowRows) * (rectHeight * colSpace);

        return { x, y };
    };

    const formatData = (csvData) => {
        console.log(csvData);
        const numberOfTransactions = 100;
        const sampledData = sampleSize(csvData, numberOfTransactions);

        const formattedData = sampledData.map((item, i) => {
            const price = item.price ? +item.price : +item.post_tax_amount; //this is the price that will be displayed to the screen
            const priceRounded = roundToNickel(price); //this is the price that is rounded to the nearest nickel
            const remainder = round(price - priceRounded, 2); //this calculates how much is gained or lost by rounding to the nearest nickel
            const category = item.category; //category of item (lombra only)
            const color = colorScale(remainder);
            const { x, y } = getGridPositions(sampledData.length, i);

            return {
                price,
                priceRounded,
                remainder,
                ...(category && { category }),
                color,
                x,
                y,
            };
        });

        console.log(formattedData);
    };

    formatData(lombraData);
    formatData(whaplesData);
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
    </svg>
{/if}
