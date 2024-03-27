<script lang="ts">
    import FaPencilAlt from "svelte-icons/fa/FaPencilAlt.svelte";
    import FaEraser from "svelte-icons/fa/FaEraser.svelte";
    import { onMount } from "svelte";

    let draw = true;
    let red = 0;
    let green = 0;
    let blue = 0;
    let color = "#000000";

    let canvas: HTMLCanvasElement;
    let context: CanvasRenderingContext2D | null;
    onMount(() => {
        context = canvas.getContext("2d");
    });

    let isDragging = false;

    // Helper Function to change rgb to hex color
    function rgbToHex() {
        let r_hex = Math.abs(red).toString(16);
        let g_hex = Math.abs(green).toString(16);
        let b_hex = Math.abs(blue).toString(16);

        if (r_hex.length == 1) r_hex = "0" + r_hex;
        if (g_hex.length == 1) g_hex = "0" + g_hex;
        if (b_hex.length == 1) b_hex = "0" + b_hex;

        return "#" + r_hex + g_hex + b_hex;
    }

    function handleStart(event: MouseEvent) {
        isDragging = true;
        if (context != null) {
            if (draw) {
                context.lineWidth = 5;
                context.strokeStyle = `rgb(${red} ${green} ${blue})`;
                context.beginPath();
                context.moveTo(event.offsetX, event.offsetY);
            } else {
                isDragging = true;
                context.lineWidth = 15;
                context.strokeStyle = `rgb(255 255 255)`;
                context.beginPath();
                context.moveTo(event.offsetX, event.offsetY);
            }
        }
    }

    function handleMove(event: MouseEvent) {
        if (isDragging && context != null) {
            context.lineTo(event.offsetX, event.offsetY);
            context.stroke();
        }
    }

    function handleEnd() {
        isDragging = false;
    }

    function handleClear() {
        if (context != null) {
            context.clearRect(0, 0, canvas.width, canvas.height);
        }
    }
</script>

<div class="mx-auto flex flex-col justify-center">
    <h1
        class="text-center text-4xl py-16 font-mono text-white bg-gradient-to-r from-indigo-500 via-purple-500 to-pink-500 mb-8"
    >
        Not-So-Fancy Paint App
    </h1>
    <div class="container mx-auto flex gap-8">
        <div class="mx-auto mb-8 flex flex-col gap-4 items-center">
            <div class="flex gap-4">
                <input
                    type="color"
                    class="p-1 ring-2 rounded h-12 w-24"
                    value={color}
                    on:input={(e) => {
                        let color = e.currentTarget.value;
                        const r = parseInt(color.substring(1, 3), 16);
                        const g = parseInt(color.substring(3, 5), 16);
                        const b = parseInt(color.substring(5, 7), 16);
                        red = r;
                        green = g;
                        blue = b;
                    }}
                />
                {#if draw}
                    <button class="active-button">
                        <FaPencilAlt />
                    </button>
                    <button
                        class="inactive-button"
                        on:click={() => (draw = !draw)}
                    >
                        <FaEraser />
                    </button>
                {:else}
                    <button
                        class="inactive-button"
                        on:click={() => (draw = !draw)}
                    >
                        <FaPencilAlt />
                    </button>
                    <button class="active-button">
                        <FaEraser />
                    </button>
                {/if}
            </div>

            <div class="flex gap-4 items-center">
                <p class="text-red-500 text-2xl text-extrabold">R</p>
                <input
                    id="red"
                    type="range"
                    min="0"
                    max="255"
                    value={red}
                    on:input={(e) => {
                        red = Number(e.currentTarget.value);
                        color = rgbToHex();
                    }}
                />
            </div>
            <div class="flex gap-4 items-center">
                <p class="text-green-500 text-2xl text-extrabold">G</p>
                <input
                    id="green"
                    type="range"
                    min="0"
                    max="255"
                    value={green}
                    on:input={(e) => {
                        green = Number(e.currentTarget.value);
                        color = rgbToHex();
                    }}
                />
            </div>
            <div class="flex gap-4 items-center">
                <p class="text-blue-500 text-2xl text-extrabold">B</p>
                <input
                    id="blue"
                    type="range"
                    min="0"
                    max="255"
                    value={blue}
                    on:input={(e) => {
                        blue = Number(e.currentTarget.value);
                        color = rgbToHex();
                    }}
                />
            </div>

            <button
                class="bg-blue-500 hover:bg-blue-700 text-white text-2xl font-bold py-2 px-4 rounded"
                on:click={handleClear}
            >
                Clear All
            </button>
        </div>
        <canvas
            width="800"
            height="600"
            bind:this={canvas}
            on:mousedown={handleStart}
            on:mousemove={handleMove}
            on:mouseup={handleEnd}
            class="border-2 rounded border-black/25"
        />
    </div>
</div>
