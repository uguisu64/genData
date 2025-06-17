<script>
    import { onMount } from "svelte";

    let { state: configState = $bindable() } = $props()

    onMount(()=>{
        configState.size ??= 50
        configState.centerX ??= 0
        configState.centerY ??= 0
        configState.std ??= 0.5
        configState.seed ??= 0
    })
</script>

<label>
    Size:
    <input type="number" bind:value={configState.size}>
</label>

<br>

<label>
    center X:
    <input type="number" bind:value={configState.centerX}>
</label>

<br>

<label>
    center Y:
    <input type="number" bind:value={configState.centerY}>
</label>

<br>

<label>
    std:
    <input type="number" bind:value={configState.std}>
</label>

<br>

<label>
    seed:
    <input type="number" bind:value={configState.seed}>
</label>

<br>