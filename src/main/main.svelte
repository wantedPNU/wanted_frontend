<script>
    import { onMount } from "svelte"; // For functions that should run on mount
    import HeroBanner from "./heroBanner.svelte";
    import downloadBlob from "../util/downloadBlob";

    let files;
    let value = '';
    let queryString = '';
    let scoreThreshold = 0.1;
    let frameInterval = 1;
    let apiUrl = "http://127.0.0.1:5000"; // Backend port: 5000

    function handleUpload() {
        if (files && files.length > 0) {
            const formData = new FormData();
            formData.append('text', value);
            formData.append('dataFile', files[0]);

            let url = `${apiUrl}/query`;

            fetch(url, {
                method: 'POST',
                body: formData
            })
            .then((response) => response.json())
            .then((result) => {
                console.log('Success:', result);
            })
            .catch((error) => {
                console.error('Error:', error);
            });
        } else {
            alert("no file selected");
            console.error('No file selected.');
        }
    }

    async function handleAddQuery(queryString) {
        let url = `${apiUrl}/query`;
        const res = await fetch(url, {
            method: 'POST',
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({ query: queryString }),
            mode: 'no-cors',
        });
    }

    async function handleStartSearch() {
        console.log("search start");
        let url = `${apiUrl}/inference`;
        const req = new Request(url, {
            method: 'GET',
        });
        const response = await fetch(req);
        const blob = await response.blob();
        console.log(blob.type);
        downloadBlob(blob, "result");
    }

    const strAsset = {
        uploadVideo: "동영상을 업로드하세요.",
        enterQuery: "실종자의 인상착의를 입력하세요",
        startSearch: "찾기"
    };
</script>

<HeroBanner />

<div class="container">
    <div class="upload-video">
        <p>{strAsset.uploadVideo}</p>
        <input id="fileUpload" type="file" bind:files>
        <button on:click={handleUpload}>업로드</button>
    </div>

    <div class="input-query">
        <p>{strAsset.enterQuery}</p>
        <input id="queryInput" bind:value={queryString} placeholder="빨간 셔츠, 민 운동화" />
        <button on:click={() => handleAddQuery(queryString)}>추가</button>
    </div>

    <div class="controls">
        <label for="scoreThreshold">Score Threshold</label>
        <input id="scoreThreshold" type="range" min="0" max="1" step="0.1" bind:value={scoreThreshold} />
        <span>{scoreThreshold}</span>

        <label for="frameInterval">Frame Interval (seconds)</label>
        <input id="frameInterval" type="range" min="1" max="5" step="1" bind:value={frameInterval} />
        <span>{frameInterval}</span>

        <button on:click={handleStartSearch}>{strAsset.startSearch}</button>
    </div>
</div>

<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
    }

    .upload-video,
    .input-query,
    .controls {
        margin-bottom: 20px;
    }

    .input-query input {
        width: 100%;
        padding: 10px;
        font-size: 16px;
        box-sizing: border-box;
    }

    .input-query button {
        padding: 10px;
        font-size: 16px;
    }

    button {
        background-color: #e57373;
        color: white;
        border: none;
        padding: 10px 20px;
        cursor: pointer;
    }

    button:hover {
        background-color: #d32f2f;
    }

    input[type="file"],
    input[type="range"] {
        display: block;
        width: 100%;
        margin-top: 10px;
    }

    label {
        display: block;
        margin-top: 10px;
    }

    span {
        display: block;
        margin-top: 5px;
    }
</style>
