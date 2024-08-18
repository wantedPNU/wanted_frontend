<script>
    let files; 
    let location='';       
    let apiUrl = "http://127.0.0.1:5000"; // Backend port: 5000
    
    function handleUpload(location) {                
        if (files && files.length > 0 && location != '') {                  
            const formData = new FormData(); //form data라는게 있길래 넣어봄.
            formData.append('location', location);
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
        } else if(!(files) && location != '') {
            alert("선택된 파일이 없습니다.");
            console.error('No file selected.');
        } else if((files) && (location == '' )){
            alert("지역이 입력되지 않았습니다.");
            console.error('No location selected.');
        } else{
            alert("지역과 파일이 입력되지 않았습니다.");
            console.error('No file and location selected.');
        }
    }

    const strAsset = {
        uploadVideo: "실종자가 있다고 의심되는 cctv 영상과 지역을 업로드하세요.",        
    };

</script>

<div class="container">
    <div class="upload-video">
        <p>{strAsset.uploadVideo}</p>
        <input id="fileUpload" type="file" bind:files>
        <input id="locationUpload" bind:value={location} placeholder="장전동">
        <button on:click={handleUpload(location)}>cctv영상 업로드</button>
    </div>
</div>



<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
    }

    .upload-video{
        margin-top: 100px
    } 

    input[id="fileUpload"] {        
        height:40px;
        margin-top: 10px;

    }

    input[id="locationUpload"] {        
        height:40px;
        margin-top: 10px;
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

</style>
