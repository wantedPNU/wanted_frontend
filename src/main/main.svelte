<script>
    import { onMount } from "svelte"; // 추후에 시작하자마자 실행해야할 함수를 추가해야한다면 사용할 예정
    import HeroBanner from "./heroBanner.svelte";

    let files;
    let value = '';    

    let query = '';
    let apiUrl="http://127.0.0.1:5000/file"; //backend port : 5000

    function getThreshold(){
        return 0.1;
    }

    function handleUpload() {
        if (files && files.length > 0) { 
            
            const formData = new FormData(); //form data라는게 있길래 넣어봄.
            formData.append('damName', value);
            formData.append('dataFile', files[0]);
            
            let url = `${apiUrl}/proc?&threshold=${getThreshold()}`;

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

    function handleAddQuery(){
        if(query.length != 0){
            alert("added query!");
        }
        else{
            alert("no query entered");
        }
        
    }

    const strAsset = {
        uploadVideo: "동영상을 업로드하세요.",  
        enterQuery:"실종자의 인상착의를 입력하세요"     
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
    <input bind:value={query} placeholder="빨간 셔츠, 운동화" />
    <button
        class="query-input-button"        
        on:click={handleAddQuery}>
    </button>    
</div>
