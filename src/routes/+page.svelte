<script lang="ts">
    import ManyRectsInManyGraphicsObjects from "$lib/ManyRectsInManyGraphicsObjects.svelte";
    import ReassembleEveryFrame from "$lib/ReassembleEveryFrame.svelte";
    import UpdateAsSingleGraphics from "$lib/UpdateAsSingleGraphics.svelte";

    let canvasWidth = 800;
    let canvasHeight = 100;
</script>

<h1>PIXI.js graphics test</h1>
<div style="width: 800px;">
    <div>
        <p>Each frame do graphics.clear() and reassemble using graphics.drawRect():</p>
        <p class="count-title">100 rects:</p>
        <ReassembleEveryFrame {canvasWidth} {canvasHeight} valuesNum={100} />
        <p class="count-title">1000 rects:</p>
        <ReassembleEveryFrame {canvasWidth} {canvasHeight} valuesNum={1000} />
        <p class="count-title">10000 rects:</p>
        <ReassembleEveryFrame {canvasWidth} {canvasHeight} valuesNum={10000} />
    </div>

    <div>
        <p>Make the graphics object once and update its x coordinate each frame:</p>
        <p class="count-title">100 rects:</p>
        <UpdateAsSingleGraphics {canvasWidth} {canvasHeight} valuesNum={100} />
        <p class="count-title">1000 rects:</p>
        <UpdateAsSingleGraphics {canvasWidth} {canvasHeight} valuesNum={1000} />
        <p class="count-title">10000 rects:</p>
        <UpdateAsSingleGraphics {canvasWidth} {canvasHeight} valuesNum={10000} />
    </div>

    <div>
        <p>Each frame do graphics.clear() and reassemble, but divide rects into several Graphics objects:</p>
        <ManyRectsInManyGraphicsObjects {canvasWidth} {canvasHeight} valuesNum={10000} />
    </div>
</div>

<style>
    .count-title {
        text-align: center;
        padding: 0;
        margin: 0;
    }
</style>
