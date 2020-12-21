<script>
    import { isArray } from "util";

    export let text;
</script>

<style>
    .scroll-container {
        color: var(--light-gray);
        display: flex;
        font-family: var(--serif);
    }

    .text-container {
        background-color: var(--dark-gray);
        flex: 2;
        font-family: var(--serif);
    }

    .graphic-container {
        flex: 5;
    }

    .section-title {
        font-size: 40px;
        font-weight: 500;
        margin: 10%;
        margin-bottom: calc(var(--base-spacing) * 2);
    }

    .paragraph {
        font-size: 24px;
        font-weight: 300;
        margin: 10%;
    }
</style>

<div class="scroll-container">
    <div class="text-container">
        <div class="section-title">{text.title}</div>
        {#each text.paragraphs as block}
            <div>
                {#if Array.isArray(block)}
                    {#each block as subParagraph}
                        <div>{subParagraph}</div>
                    {/each}
                {:else}
                    <div>{block}</div>
                {/if}
            </div>
        {/each}
    </div>
    <div class="graphic-container" />
</div>
