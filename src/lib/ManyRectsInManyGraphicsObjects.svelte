<script lang="ts">
    import { onMount } from "svelte";
    import * as PIXI from "pixi.js";
    import chroma from "chroma-js";

    let parentHTML: HTMLElement;
    export let canvasWidth: number;
    export let canvasHeight: number;
    export let valuesNum: number;


    const rectsPerGraphicsObject = 1000;
    $: batchColors = generateRandomBatchColors(valuesNum);

    //~ zooming & panning
    let dragging = false;
    let lastMousePosX: number;
    let lastMousePosY: number;
    let offsetX = 0;
    let offsetY = 0;
    let zoomScaleFactor = 1.0;

    const generateRandomBatchColors = (valuesNum: number) => {
        const colorMaps = [];
        const numOfGroups = valuesNum / rectsPerGraphicsObject;
        for (let i = 0; i < numOfGroups; i++) {
            const colorMap = chroma.scale([chroma.random(), chroma.random()]);
            colorMaps.push(colorMap);
        }
        return colorMaps;
    };

    const generateRandomValuesArr = (N: number): number[] => {
        const rndValues: number[] = [];
        for (let i = 0; i < N; i++) {
            rndValues.push(Math.random());
        }
        return rndValues;
    };

    const generateRandomColorsArr = (N: number): string[] => {
        const rndColors: string[] = [];
        for (let i = 0; i < N; i++) {
            rndColors.push(chroma.random().hex());
        }
        return rndColors;
    };

    const makeDummyHeatmapChart = (g: PIXI.Graphics, values: number[], offsetX: number, offsetY: number) => {
        //~ compute cell size based on number of values
        const boxesPerColumn = 50;
        const cWidth = (zoomScaleFactor * canvasWidth) / (values.length / boxesPerColumn);
        const cHeight = (zoomScaleFactor * canvasHeight) / boxesPerColumn;

        //~ breaking down into multiple graphics objects

        let currentGraphicsObj = null;
        let gIndex = -1;
        // let currentGraphicsObj = g;
        for (const [i, c] of values.entries()) {
            if (i % rectsPerGraphicsObject == 0) {
                //~ make new g object
                currentGraphicsObj = new PIXI.Graphics();
                gIndex += 1;
                g.addChild(currentGraphicsObj);
            }

            if (currentGraphicsObj == null) continue; //~ shouldn't happen anyway...

            // const cellColor = colorMap(c);
            const cellColor = batchColors[gIndex](c);
            currentGraphicsObj.beginFill(chroma(cellColor).num());
            currentGraphicsObj.drawRect(
                offsetX + (i / boxesPerColumn) * cWidth,
                offsetY + (i % boxesPerColumn) * cHeight,
                cWidth,
                cHeight,
            );
            currentGraphicsObj.endFill();
        }
    };

    const onPointerDown = (e) => {
        // console.log("down");
        lastMousePosX = e.data.global.x;
        lastMousePosY = e.data.global.y;
        dragging = true;
    };

    const onPointerMove = (e) => {
        if (!dragging) return;
        // console.log("move");
        const mousePosX = e.data.global.x;
        const mousePosY = e.data.global.y;

        offsetX += mousePosX - lastMousePosX;
        offsetY += mousePosY - lastMousePosY;
        lastMousePosX = mousePosX;
        lastMousePosY = mousePosY;
    };

    const onPointerUp = (e) => {
        // console.log("up");
        dragging = false;
    };

    const onWheel = (e) => {
        console.log(e);

        zoomScaleFactor += 0.001 * e.deltaY;
    };

    onMount(() => {
        let app = new PIXI.Application({ width: canvasWidth, height: canvasHeight });
        parentHTML.appendChild(app.view);

        app.stage.hitArea = app.screen;
        app.stage.interactive = true;
        app.stage.on("pointerdown", onPointerDown);
        app.stage.on("pointerup", onPointerUp);
        app.stage.on("pointermove", onPointerMove);
        // app.stage.on("wheel", () => {console.log("wheel");}); // does not fire!

        app.view.addEventListener("wheel", onWheel);

        const values = generateRandomValuesArr(valuesNum);
        const colors = generateRandomColorsArr(valuesNum);

        let graphics = new PIXI.Graphics();

        app.stage.addChild(graphics);

        //~ frame loop
        const render = (frametime: number) => {
            const removed = graphics.removeChildren();
            for (const obj of removed) {
                obj.destroy();
            }
            graphics.clear();

            makeDummyHeatmapChart(graphics, values, offsetX, offsetY);

            requestAnimationFrame(render);
        };
        const requestID = requestAnimationFrame(render);
    });
</script>

<div bind:this={parentHTML}></div>
