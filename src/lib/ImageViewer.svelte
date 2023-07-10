<script lang="ts">
    import { cubicInOut } from "svelte/easing";
    import { tweened } from "svelte/motion";

    const [actualWidth, actualHeight] = [1600, 1600];
    const [initalWidth, initalHeight] = [actualWidth / 2, actualHeight / 2];
    const src = `https://source.unsplash.com/random/${initalWidth}x${initalHeight}?space`;

    let mouseX = 0;
    let mouseY = 0;

    function handleMouseMove(mouseEvent: MouseEvent) {
        mouseX = (mouseEvent.offsetX / initalWidth) * 100;
        mouseY = (mouseEvent.offsetY / initalHeight) * 100;
    }

    const zoom = tweened(1, {
        duration: 150,
        easing: cubicInOut,
    });

    let zoomed = false;
    $: {
        if (zoomed) {
            zoom.set(2);
        } else {
            zoom.set(1);
        }
    }
</script>

<div
    on:click={() => (zoomed = !zoomed)}
    on:keydown={() => (zoomed = !zoomed)}
    on:mousemove={handleMouseMove}
    on:mouseleave={() => (zoomed = false)}
    role="button"
    tabindex="0"
    style:width={initalWidth}
    style:height={initalHeight}
    draggable="false"
>
    <img
        width={initalWidth}
        height={initalHeight}
        {src}
        alt="art from unsplash"
        class:zoomed
        style:scale={$zoom}
        style:transform-origin="{mouseX}% {mouseY}% 0px"
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
