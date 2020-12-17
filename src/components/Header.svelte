<script>
</script>

<style>
    .header {
        align-items: center;
        color: var(--light-gray);
        background-color: var(--dark-gray);
        display: flex;
        flex-direction: column;
        padding-top: var(--padding-desktop);
        padding-bottom: var(--padding-desktop);
    }

    .header-content {
        display: flex;
        justify-content: space-between;
        margin-bottom: calc(var(--base-spacing) * 2);
        width: 80%;
    }

    .penny-image {
        width: 450px;
        height: 450px;
    }

    .title-container {
        width: 635px;
    }

    .title {
        font-family: "Libre Franklin", "Franklin Gothic", sans-serif;
        font-weight: 700;
        font-size: 128px;
        line-height: 128px;
    }

    .subhead {
        font-family: var(--serif);
        font-size: 64px;
        margin-bottom: calc(var(--base-spacing));
    }

    .byline {
        font-family: var(--sans-serif);
        font-size: 24px;
    }

    .hook {
        font-family: var(--serif);
        font-size: 32px;
        line-height: 40px;
    }
</style>

<div class="header">
    <div class="header-content">
        <img class="penny-image" src="assets/penny.png" alt="penny" />
        <div class="title-container">
            <div class="title">In Pennies We Trust</div>
            <div class="subhead">But Should We?</div>
            <div class="byline">by Dan Spector</div>
        </div>
    </div>
    <div class="hook">
        Millions of Americans still love the penny.
        <br />
        But are they aware of its drawbacks?
    </div>
</div>
