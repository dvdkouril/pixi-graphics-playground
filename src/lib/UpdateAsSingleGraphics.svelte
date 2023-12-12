<script lang="ts">
    import { onMount } from "svelte";
    import * as PIXI from "pixi.js";
    import chroma from "chroma-js";

    let parentHTML: HTMLElement;
    export let canvasWidth: number;
    export let canvasHeight: number;
    export let valuesNum: number;

    const generateRandomValuesArr = (N: number, MAX: number): number[] => {
        const rndValues: number[] = [];
        for (let i = 0; i < N; i++) {
            rndValues.push(Math.random() * MAX);
        }
        return rndValues;
    };

    const generateRandomBarChart = (g: PIXI.Graphics) => {
        const rndValues = generateRandomValuesArr(valuesNum, canvasHeight);
        const cWidth = canvasWidth / rndValues.length;

        for (const [i, c] of rndValues.entries()) {
            const rndColor = chroma.random();
            g.beginFill(rndColor.hex());
            g.drawRect(0 + i * cWidth, 0, cWidth, c);
            g.endFill();
        }
    };

    onMount(() => {
        // Create the application helper and add its render target to the page
        let app = new PIXI.Application<HTMLCanvasElement>({ width: canvasWidth, height: canvasHeight });
        parentHTML.appendChild(app.view);

        // Create the sprite and add it to the stage
        let graphics = new PIXI.Graphics();
        app.stage.addChild(graphics);

        generateRandomBarChart(graphics);

        // Add a ticker callback to move the sprite back and forth
        let elapsed = 0.0;
        app.ticker.add((delta) => {
            // console.log("delta = " + delta);
            elapsed += delta;
            // sprite.x = 100.0 + Math.cos(elapsed / 50.0) * 100.0;
            graphics.x = 100.0 + Math.cos(elapsed / 50.0) * 100.0;
        });
    });
</script>

<div bind:this={parentHTML}></div>