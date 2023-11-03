<script>
    import { push } from 'svelte-spa-router'
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"

    let error = {detail:[]}
    let fileInput;
    let name = "";

    function handleSubmit() {
        event.preventDefault()
        let url = "/api/model/upload"

        if (!fileInput.files[0]) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("name", name);
        for(let i = 0; i < fileInput.files.length; i++) {
            formData.append("files", fileInput.files[i]);
        }

        fastapi(
            "post", url, formData,
            (json) => {
                alert("File uploaded successfully!");
                push('/dashboard');
            },
            (error) => {
                alert("File upload failed: " + JSON.stringify(error));
            }
        );
    }

</script>

<div class="container">
    <h5 class="my-3 border-bottom pb-2">모델 업로드</h5>
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <label for="name">제목</label>
            <input type="text" bind:value={name} />
        </div>
        <div class="mb-3">
            <label for="content">내용</label>
            <input type="file" bind:this={fileInput} multiple/>
        </div>
        <button class="btn btn-primary" on:click="{handleSubmit}">저장하기</button>
    </form>
</div>