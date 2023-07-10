<script lang="ts">
    import { cubicInOut } from "svelte/easing";
    import { tweened } from "svelte/motion";

    interface Dimensions {
        width: number;
        height: number;
    }

    interface PercentageXY {
        percentX: number;
        percentY: number;
    }

    const imageScaleWhenZoomed = 2;

    const imageSourceSize: Dimensions = {
        width: 1500,
        height: 1500,
    };

    const imageSizeOnPage: Dimensions = {
        width: imageSourceSize.width / imageScaleWhenZoomed,
        height: imageSourceSize.height / imageScaleWhenZoomed,
    };

    const src = `https://source.unsplash.com/random/${imageSourceSize.width}x${imageSourceSize.height}?sushi`;

    let mousePositionOnImage: PercentageXY = {
        percentX: 0,
        percentY: 0,
    };

    function handleMouseMove(mouseEvent: MouseEvent) {
        mousePositionOnImage.percentX =
            (mouseEvent.offsetX / imageSizeOnPage.width) * 100;
        mousePositionOnImage.percentY =
            (mouseEvent.offsetY / imageSizeOnPage.height) * 100;
    }

    const imageScale = tweened(1, {
        duration: 150,
        easing: cubicInOut,
    });

    let zoomed = false;
    $: imageScale.set(zoomed ? imageScaleWhenZoomed : 1);
</script>

<div
    on:click={() => (zoomed = !zoomed)}
    on:keypress={() => (zoomed = !zoomed)}
    on:mouseleave={() => (zoomed = false)}
    on:mousemove={handleMouseMove}
    role="button"
    tabindex="0"
    style:width={imageSizeOnPage.width}
    style:height={imageSizeOnPage.height}
>
    <img
        {src}
        width={imageSizeOnPage.width}
        height={imageSizeOnPage.height}
        alt="art from unsplash"
        class:zoomed
        style:scale={$imageScale}
        style:transform-origin="{mousePositionOnImage.percentX}% {mousePositionOnImage.percentY}%
        0px"
        draggable="false"
    />
</div>

<style>
    img:hover {
        cursor: zoom-in;
    }

    .zoomed:hover {
        cursor: zoom-out;
    }

    div {
        overflow: hidden;
    }
</style>
