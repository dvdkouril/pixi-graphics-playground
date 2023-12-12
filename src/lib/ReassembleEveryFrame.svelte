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

    const generateRandomColorsArr = (N: number): string[] => {
        const rndColors: string[] = [];
        for (let i = 0; i < N; i++) {
            rndColors.push(chroma.random().hex());
        }
        return rndColors;
    };

    const makeBarChart = (g: PIXI.Graphics, values: number[], colors: string[], offset: number) => {
        const cWidth = canvasWidth / values.length;
        for (const [i, c] of values.entries()) {
            g.beginFill(chroma(colors[i]).num());
            g.drawRect(offset + i * cWidth, 0, cWidth, c);
            g.endFill();
        }
    };

    onMount(() => {
        let app = new PIXI.Application({ width: canvasWidth, height: canvasHeight });
        parentHTML.appendChild(app.view);

        const values = generateRandomValuesArr(valuesNum, canvasHeight);
        const colors = generateRandomColorsArr(valuesNum);

        let graphics = new PIXI.Graphics();

        app.stage.addChild(graphics);

        //~ frame loop
        let elapsed = 0.0;
        const render = (frametime: number) => {
            // console.log("frametime = " + frametime);

            graphics.clear();
            graphics.removeChildren();

            elapsed += frametime;
            const offset = Math.cos(frametime / 10.0 / 50.0) * 100.0;
            makeBarChart(graphics, values, colors, offset);

            requestAnimationFrame(render);
        };
        const requestID = requestAnimationFrame(render);
    });
</script>

<div bind:this={parentHTML}></div>
