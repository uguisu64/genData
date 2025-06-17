<script>
    import { onMount } from "svelte";

    let { state: configState = $bindable() } = $props()

    const psdList = [
        "nomal",
        "uniform",
        "laplace",
        "exp",
        "gamma",
        "beta",
        "chisquare"
    ]

    onMount(() => {
        configState.x.type ??= "nomal"
        configState.x.seed ??= 0
        configState.x.loc ??= 0
        configState.x.scale ??= 1
        configState.x.size ??= 100
        configState.x.low ??= -1
        configState.x.high ??= 1
        configState.x.shape ??= 2.0
        configState.x.a ??= 0.5
        configState.x.b ??= 0.5
        configState.x.df ??= 2

        configState.y.type ??= "nomal"
        configState.y.seed ??= 0
        configState.y.loc ??= 0
        configState.y.scale ??= 1
        configState.y.size ??= 100
        configState.y.low ??= -1
        configState.y.high ??= 1
        configState.y.shape ??= 2.0
        configState.y.a ??= 0.5
        configState.y.b ??= 0.5
        configState.y.df ??= 2
    })

    function forceUpdate() {
        configState = { ...configState };
    }
</script>

<label>
    X axis:
    <select bind:value={configState.x.type}>
        {#each psdList as psd}
            <option value={psd}>{psd}</option>
        {/each}
    </select>
</label>

{#if configState.x.type === "nomal"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        loc:
        <input type="number" bind:value={configState.x.loc}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.x.scale}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "uniform"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        low:
        <input type="number" bind:value={configState.x.low}>
    </label>
    <br>
    <label>
        high:
        <input type="number" bind:value={configState.x.high}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "laplace"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        loc:
        <input type="number" bind:value={configState.x.loc}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.x.scale}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "exp"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.x.scale}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "gamma"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        shape:
        <input type="number" bind:value={configState.x.shape}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.x.scale}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "beta"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        a:
        <input type="number" bind:value={configState.x.a}>
    </label>
    <br>
    <label>
        b:
        <input type="number" bind:value={configState.x.b}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{:else if configState.x.type === "chisquare"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.x.size}>
    </label>
    <br>
    <label>
        df:
        <input type="number" bind:value={configState.x.df}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.x.seed}>
    </label>
{/if}

<br>

<label>
    Y axis:
    <select bind:value={configState.y.type} onchange={forceUpdate}>
        {#each psdList as psd}
            <option value={psd}>{psd}</option>
        {/each}
    </select>
</label>

{#if configState.y.type === "nomal"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        loc:
        <input type="number" bind:value={configState.y.loc} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.y.scale} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "uniform"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        low:
        <input type="number" bind:value={configState.y.low} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        high:
        <input type="number" bind:value={configState.y.high} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "laplace"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        loc:
        <input type="number" bind:value={configState.y.loc} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.y.scale} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "exp"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.y.scale} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "gamma"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        shape:
        <input type="number" bind:value={configState.y.shape} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        scale:
        <input type="number" bind:value={configState.y.scale} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "beta"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        a:
        <input type="number" bind:value={configState.y.a} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        b:
        <input type="number" bind:value={configState.y.b} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{:else if configState.y.type === "chisquare"}
    <br>
    <label>
        size:
        <input type="number" bind:value={configState.y.size} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        df:
        <input type="number" bind:value={configState.y.df} onchange={forceUpdate}>
    </label>
    <br>
    <label>
        seed:
        <input type="number" bind:value={configState.y.seed} onchange={forceUpdate}>
    </label>
{/if}

<br>