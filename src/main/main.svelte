<script>
    import { onMount } from "svelte"; // 추후에 시작하자마자 실행해야할 함수를 추가해야한다면 사용할 예정
    import HeroBanner from "./heroBanner.svelte";
    import downloadBlob from "../util/downloadBlob"
    let files;
    let value = '';    

    let queryString = '';    

    let apiUrl="http://127.0.0.1:5000"; //backend port : 5000

    function getThreshold(){
        return 0.1;
    }

    function handleUpload() {
        if (files && files.length > 0) { 
            
            const formData = new FormData(); //form data라는게 있길래 넣어봄.
            formData.append('text', value);
            formData.append('dataFile', files[0]);
            
            // let url = `${apiUrl}/proc?&threshold=${getThreshold()}`;            
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
    
    async function handleAddQuery(queryString){               
        let url = `${apiUrl}/query`;                               
        const res = await fetch(url, {
			method: 'POST',
            headers: {
                    "Content-Type": "application/json",
            },
			body: queryString,
            mode : 'no-cors',
		})  
    }

    async function handleStartSearch(){
        console.log("search start");
        let url = `${apiUrl}/inference`;                
        const req = new Request(url,{
            method: 'GET',                                        
        });
        const response = await fetch(req)        
        const blob = await response.blob();                
        console.log(blob.type);        
        downloadBlob(blob, "result");        
    }

    const strAsset = {
        uploadVideo: "동영상을 업로드하세요.",  
        enterQuery:"실종자의 인상착의를 입력하세요",
        startSearch:"실종자 찾기 시작"
    };
    

</script>

<HeroBanner />

<div class="upload-video">
    <p>{strAsset.uploadVideo}</p>
    <input id="fileUpload" type="file" bind:files>      
    <button
        class="video-upload-button"
        on:click={handleUpload}>        
    </button>  
</div>

<div class="input-query">    
    <p>{strAsset.enterQuery}</p>
    <input bind:value={queryString} placeholder="빨간 셔츠, 운동화" />
    <button
        class="query-input-button"        
        on:click={handleAddQuery(queryString)}>
    </button>    
</div>
<!-- <form on:submit|preventDefault={handleSubmit}>
    <input type="text" bind:value={queryString} placeholder="쿼리 입력" required />
    <button type="submit">저장</button>
</form> -->
<div class="start-search">    
    <p>{strAsset.startSearch}</p>    
    <button
        class="start-search-button"        
        on:click={handleStartSearch}>
    </button>    
</div>