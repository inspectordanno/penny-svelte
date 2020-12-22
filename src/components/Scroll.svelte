<script>
    export let text;
    export let topic;
    export let activeBlock;

    let graphicContainerWidth;
    let graphicContainerHeight;

    //sets data attribute of block
    const getDataAttribute = (block, i) => {
        if (block.description) {
            return `${topic}-${block.description}`;
        }
        return `${topic}-${i}`;
    };
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
        margin: 32px;
        margin-bottom: calc(var(--base-spacing) * 2);
    }

    .block {
        font-size: 24px;
        font-weight: 300;
        margin: 0px 32px 200px;
        opacity: 0.5;
    }

    .active-block {
        opacity: 1;
    }

    .sub-paragraph {
        margin-bottom: 32px;
    }
</style>

<div class="scroll-container">
    <div class="text-container">
        <div class="section-title">{text.title}</div>
        {#each text.paragraphs as block, i}
            <div
                class="block {activeBlock === getDataAttribute(block, i) ? 'active-block' : ''}"
                data-step={getDataAttribute(block, i)}>
                {#if Array.isArray(block)}
                    {#each block as subParagraph, ii}
                        <div class:sub-paragraph={ii !== block.length - 1}>
                            {@html subParagraph}
                        </div>
                    {/each}
                {:else}
                    <div>
                        {@html block}
                    </div>
                {/if}
            </div>
        {/each}
    </div>
    <div
        bind:clientWidth={graphicContainerWidth}
        bind:clientHeight={graphicContainerHeight}
        class="graphic-container">
        <slot />
    </div>
</div>
