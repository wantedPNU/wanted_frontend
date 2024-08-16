<script>    
    import HeroBanner from "./heroBanner.svelte";      
	import Tooltip from "./Tooltip.svelte"; // Tooltip 컴포넌트 가져오기
    
    import { Progressbar } from 'flowbite-svelte';

    let files;    
    let local;
    let queryString = '';
    let scoreThreshold = 0.02;
    let frameInterval = 3;
    let apiUrl = "http://127.0.0.1:5000"; // Backend port: 5000

    let processing = false; // 상태 변수 추가
    let completed = false; // 상태 변수 추가
    let isNotReadyForSearch = false;
    let resultFileUrl = ''; // 결과 파일 URL

    function handleUpload() {        
        if (files && files.length > 0) {             
            const formData = new FormData(); //form data라는게 있길래 넣어봄.
            formData.append('filename', "testfile");
            formData.append('file', files[0]);                                            

            let url = `${apiUrl}/video/file`;              
            
            fetch(url, {
                method: 'POST',                        
                body:formData,
            })
            .then((response) => response.json())
            .then((result) => {
                alert("탐색할 동영상이 업로드 되었습니다.");
                console.log('Success:', result);
            })
            .catch((error) => {
                console.error('Error:', error);
            });
        } else {
            alert("선택된 파일이 없습니다.");
            console.error('No file selected.');
        }
    }

    async function handleAddQuery(queryString) {
        let url = `${apiUrl}/query`;
        if(queryString.length > 0){
            const res = await fetch(url, {
            method: 'POST',
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({ query: queryString }),
            mode: 'no-cors',
            }).then((result) => {
                alert("탐색할 대상을 추가했습니다.");                
            });            
        }else{
            alert("쿼리가 비었습니다.");
            console.error('No query added.');
        }
        
    }

    async function handleStartSearch() {
        processing = true; // 처리 중 상태로 변경
        completed = false; // 완료 상태 초기화

        console.log("search start");
        let url = `${apiUrl}/inference?scoreThreshold=${scoreThreshold}&frameInterval=${frameInterval}`;
        const req = new Request(url, {
            method: 'GET',
        });
        const response = await fetch(req);
        const blob = await response.blob();
        console.log(blob.type);

        // Blob을 URL로 변환하여 저장
        resultFileUrl = URL.createObjectURL(blob);
        processing = false; // 처리 중 상태 해제
        completed = true; // 완료 상태로 변경
    }
    

    const strAsset = {
        uploadVideo: "1. 실종자가 있다고 의심되는 cctv 영상을 업로드하세요.",
        selectLocal:"1. 실종자가 발생한 지역을 선택하세요(개발중)",
        enterQuery: "2. 실종자의 인상착의를 입력하세요",
        controlParameter: "3. Threshold와 Frame Interval을 설정하세요",
        startSearch: "찾기"
    };
</script>

<HeroBanner />

<div class="container">
    <!-- <div class="upload-video">
        <p>{strAsset.uploadVideo}</p>
        <input id="fileUpload" type="file" bind:files>
        <button on:click={handleUpload}>cctv영상 업로드</button>
    </div> -->
    <div class="select-Local">
        <p>{strAsset.selectLocal}</p>
        <input id="fileUpload" bind:value={local}>
        <button on:click={handleUpload}>지역 제출</button>
    </div>
    <div class="input-query">
        <p>{strAsset.enterQuery}</p>
        <input id="queryInput" bind:value={queryString} placeholder="빨간 셔츠, 흰 운동화" />
        <button on:click={() => handleAddQuery(queryString)}>실종자 인상착의 제출</button>
    </div>

    <div class="controls">
        <p>{strAsset.controlParameter}</p>
        <Tooltip title="Score Threshold는 YOLO 모델이 객체를 인식할 때, 해당 객체가 무엇인지 확신하는 정도를 결정하는 값입니다. 이 값이 높을수록 모델이 확신하는 경우에만 객체로 인식하며, 낮을수록 더 많은 객체를 인식하지만 그만큼 오탐지가 발생할 가능성도 높아집니다">
            <label for="scoreThreshold">Threshold 설정</label>
        </Tooltip>
        <input id="scoreThreshold" type="range" min="0" max="1" step="0.002" bind:value={scoreThreshold} />
        <span>{scoreThreshold}</span>

        <Tooltip title="Frame Interval은 YOLO 모델이 비디오를 분석할 때, 몇 프레임마다 객체 인식을 수행할지를 결정하는 값입니다. 이 값이 클수록 더 적은 프레임에서만 분석을 하여 처리 속도가 빨라지지만, 중요한 장면을 놓칠 가능성이 높아질 수 있습니다.">
            <label for="frameInterval">Frame Interval(초 단위)</label>
        </Tooltip>
        <input id="frameInterval" type="range" min="1" max="5" step="1" bind:value={frameInterval} />
        <span>{frameInterval}</span>

        {#if processing}
            <button disabled>처리 중...</button>
            <Progressbar progress="50" />
        {:else if completed}
            <button on:click={handleStartSearch}>{strAsset.startSearch}</button>
            <p>탐색 완료!</p>
            <div class="result-download">
                <a href={resultFileUrl} download="result.zip">
                    <button>다운로드</button>
                </a>
            </div>
        {:else}
            <button id="searchStart" disbled='{isNotReadyForSearch}'on:click={handleStartSearch}>{strAsset.startSearch}</button>
        {/if}
    </div>
</div>



<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
    }

    /* .upload-video{
        margin-top: 100px
    } */
    .input-query{
        margin-top: 100px
    }
    .controls {
        margin-top: 100px;
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

    button:disabled {
        background-color: #e0e0e0;
        cursor: not-allowed;
    }

    /* input[type="file"], */
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

    .result-download {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-top: 20px;
    }

    .result-download button {
        background-color: #4caf50;
        padding: 10px 20px;
        color: white;
    }

    .result-download button:hover {
        background-color: #388e3c;
    }
</style>
