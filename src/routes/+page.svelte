<script>
    import { loadPyodideInstance } from "$lib/loadPyodide";
    import { onMount } from "svelte";
    import MakeBlob from "./makeBlob.svelte";
    import MakeFree from "./makeFree.svelte";
    import DrawGraph from "./DrawGraph.svelte";
    import { calcNomal, calcUniform, calcLaplace, calcExp, calcGamma, calcBeta, calcChisquare, makeBlob } from "$lib/pythonCodes";

    let settingObject = $state([])
    let fileName = $state("my-cluster")

    let pyodideInstance = $state(null)

    let results = $state(null)

    const psdMapping = {
        "nomal": calcNomal,
        "uniform": calcUniform,
        "laplace": calcLaplace,
        "exp": calcExp,
        "gamma": calcGamma,
        "beta": calcBeta,
        "chisquare": calcChisquare
    }

    onMount(async() => {
        pyodideInstance = await loadPyodideInstance()
        await pyodideInstance.loadPackage("numpy")
        await pyodideInstance.loadPackage("scikit-learn")
    })

    function addCluster() {
        const newCluster = {
            type: "makeblob",
            state: {},
            stateFree: {x: {}, y: {}}
        }
        settingObject.push(newCluster)
    }

    $effect(async() => {
        settingObject
        results = await generateData()
    })

    async function generateData() {
        const res = await Promise.all(settingObject.map(async(obj, i) => {
            if (obj.type === "makeblob") {
                let context = pyodideInstance.toPy(obj.state)
                const resPy = await pyodideInstance.runPythonAsync(makeBlob, { globals: context})
                const res = {...resPy.toJs({ dict_converter: Object.fromEntries }), i: i}
                return res
            }
            else if (obj.type === "free") {
                const contextX = pyodideInstance.toPy(obj.stateFree.x)
                const resX = await pyodideInstance.runPythonAsync(psdMapping[obj.stateFree.x.type], {globals: contextX})
                const contextY = pyodideInstance.toPy(obj.stateFree.y)
                const resY = await pyodideInstance.runPythonAsync(psdMapping[obj.stateFree.y.type], {globals: contextY})
                const res = { x: resX.toJs({ dict_converter: Object.fromEntries }), y: resY.toJs({ dict_converter: Object.fromEntries }), i: i}
                return res
            }
        }))
        return res
    }

    function createDataFileContent() {
        const allPoints = [];

        results.forEach(cluster => {
            for (let i = 0; i < cluster.x.length; i++) {
                allPoints.push([cluster.x[i], cluster.y[i]]);
            }
        });

        let content = `${allPoints.length}\t2\n`;
        const pointLines = allPoints.map(p => `${p[0]}\t${p[1]}`);
        content += pointLines.join('\n');
        
        return content;
    }

    function createLabelFileContent() {
        const allLabels = [];
        results.forEach(cluster => {
            for (let i = 0; i < cluster.x.length; i++) {
                allLabels.push(cluster.i);
            }
        });

        const numClusters = results.length;
        const lines = [];

        for (let k = 0; k < numClusters; k++) {
            const membershipRow = allLabels.map(label => (label === k ? 1 : 0));
            
            lines.push(membershipRow.join(' '));
        }
        
        return lines.join('\n');
    }

    function triggerDownload(content, fileName, mimeType) {
        const blob = new Blob([content], { type: mimeType });
        const url = URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.download = fileName;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
    }

    function handleDataDownload() {
        const fileContent = createDataFileContent();
        triggerDownload(fileContent, `${fileName}.dat`, 'text/plain');
    }

    function handleLabelDownload() {
        const fileContent = createLabelFileContent();
        triggerDownload(fileContent, `${fileName}.correctCrispMembership`, 'text/plain');
    }
</script>


<div class="container">
    <div class="settings-panel">
        <h2>settings</h2>
        {#each settingObject as set}
            <div class="config-group">
                <select bind:value={set.type}>
                    <option value="makeblob">makeblob</option>
                    <option value="free">free</option>
                </select>
                <br>
                {#if set.type === "makeblob"}
                    <MakeBlob bind:state={set.state}></MakeBlob>
                {:else}
                    <MakeFree bind:state={set.stateFree}></MakeFree>
                {/if}
            </div>
        {/each}
        <br>
        <button onclick={addCluster}>+</button>
        <div class="config-group">
            <h3>Export Results</h3>
            <div class="form-item">
                <label for="filename">File Name:</label>
                <input type="text" id="filename" bind:value={fileName} placeholder="ファイル名を入力...">
            </div>
            <button onclick={handleDataDownload}>
                Download Data (.dat)
            </button>
            <button onclick={handleLabelDownload}>
                Download Labels (.correctCrispMembership)
            </button>
        </div>
    </div>
    <div class="main-content">
        {#if results}
            {#key results}
                <DrawGraph results={results}></DrawGraph>
            {/key}
        {/if}
    </div>
</div>

<style>
    .container {
        display: flex;
        height: 100vh;
    }

    .settings-panel {
        width: 300px;
        padding: 20px;
        background-color: #f4f4f9;
        flex-shrink: 0; 
        overflow-y: auto;
    }

    .main-content {
        flex-grow: 1; 
        padding: 20px;
        background-color: #ffffff;
    }

    .config-group {
        background-color: #ffffff; /* カードの背景は白 */
        
        /* 1. 余白 (最も重要) */
        padding: 16px; /* カードの内側の余白 */
        margin-bottom: 20px; /* カード間の外側の余白 */

        /* 2. 角丸 */
        border-radius: 8px; /* 角を丸くすると柔らかい印象になる */

        /* 3. 影 (ユーザーの要望) */
        /* 薄くて自然な影を付けるのがポイント */
        box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
        
        /* 4. (オプション) 枠線 */
        /* 影と組み合わせると境界がより明確になる */
        border: 1px solid #e2e8f0; 
    }

    .form-item {
        display: flex;
        align-items: center;
        gap: 8px;
        margin-bottom: 12px;
    }
    .form-item input {
        flex-grow: 1;
    }
</style>