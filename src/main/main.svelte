<script>
    import { onMount } from "svelte"; // 추후에 시작하자마자 실행해야할 함수를 추가해야한다면 사용할 예정
    import HeroBanner from "./heroBanner.svelte";

    let files;
    let value = '';    

    let queryString = '';
    let query = {
            "text": "woman with blue shirt, man with white shirt",
        };

    let apiUrl="http://127.0.0.1:5000"; //backend port : 5000

    function getThreshold(){
        return 0.1;
    }

    let foo = 'baz'
	let bar = 'qux'
	let result = null
	
	async function doPost () {
        let name 
        let jsonsff = {
            name : "hello"
        }
        console.log(typeof(JSON.stringify(jsonsff)))
		const res = await fetch(apiUrl, {
			method: 'POST',
            headers: {
                    "Content-Type": "application/json",
            },
			body: JSON.stringify({
                name : "hello"
            }), 
            mode : 'no-cors',
		})
		
		// const json = await res.json()
		// result = JSON.stringify(json)
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
        console.log(JSON.stringify(query));
        console.log(typeof(queryString));
        let jsonff = JSON.stringify(query);
        console.log(typeof(jsonff));
        const res = await fetch(apiUrl, {
			method: 'POST',
            headers: {
                    "Content-Type": "application/json",
            },
			body: JSON.stringify({
                text : "hello"
            }), 
            mode : 'no-cors',
		})  
    }

    async function handleStartSearch(){
        console.log("search start");
        let url = `${apiUrl}/inference`;
        alert("added query!");
        console.log("search start");
        const req = new Request(url,{
            method: "GET",           
            mode:'no-cors' ,
        });
        const response = await fetch(req);

        console.log(response.status);
    }

    const strAsset = {
        uploadVideo: "동영상을 업로드하세요.",  
        enterQuery:"실종자의 인상착의를 입력하세요",
        startSearch:"실종자 찾기 시작"
    };

    async function handleSubmit() {
        await saveQueryToDB(queryString);
    }
    

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