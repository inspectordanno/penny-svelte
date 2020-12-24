<script>
    import sampleSize from "lodash/sampleSize";
    import round from "lodash/round";
    import orderBy from "lodash/orderBy";
    import meanBy from "lodash/meanBy";
    import { activeBlock, graphicContainerWidth } from "../store/store";
    import lombraData from "../../public/assets/lombra_sample.csv";
    import whaplesData from "../../public/assets/whaples_sample.csv";
    import App from "./App.svelte";

    const roundToNickel = (price) => Math.round(price * 20) / 20;

    const formatData = (csvData) => {
        console.log(csvData);
        const numberOfTransactions = 100;
        const sampledData = sampleSize(csvData, numberOfTransactions);

        const formattedData = sampledData.map((item) => {
            const price = item.price ? +item.price : +item.post_tax_amount; //this is the price that will be displayed to the screen
            const priceRounded = roundToNickel(price); //this is the price that is rounded to the nearest nickel
            const remainder = round(price - priceRounded, 2); //this calculates how much is gained or lost by rounding to the nearest nickel
            const category = item.category; //category of item (lombra only)

            return {
                price,
                priceRounded,
                remainder,
                ...(category && { category }),
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
