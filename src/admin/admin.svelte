<script>
    let files;    
    
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
    const strAsset = {
        uploadVideo: "실종자가 있다고 의심되는 cctv 영상을 업로드하세요.",
        enterQuery: "2. 실종자의 인상착의를 입력하세요",
        controlParameter: "3. Threshold와 Frame Interval을 설정하세요",
        startSearch: "찾기"
    };

</script>

<div class="container">
    <div class="upload-video">
        <p>{strAsset.uploadVideo}</p>
        <input id="fileUpload" type="file" bind:files>
        <button on:click={handleUpload}>cctv영상 업로드</button>
    </div>
</div>