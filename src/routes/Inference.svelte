<script>
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"
    import { link, push } from 'svelte-spa-router'
    import { is_login, username } from "../lib/store"

    export let params = {}

    let error = {detail:[]}
    let model_uuid = params.model_uuid
    let fileInput;
    let name = "";

    function handleSubmit() {
        event.preventDefault()
        let url = "/api/model/inference/" + model_uuid

        if (!fileInput.files[0]) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("name", name);
        formData.append("file", fileInput.files[0]);

        fastapi(
            "post", url, formData,
            (json) => {
                alert(JSON.stringify(json));
            },
            (error) => {
                alert("File upload failed: " + JSON.stringify(error));
            }
        );
    }

</script>

<div class="container">
    <h5 class="my-3 border-bottom pb-2">질문 등록</h5>
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <label for="content">내용</label>
            <input type="file" bind:this={fileInput} multiple/>
        </div>
        <button class="btn btn-primary" on:click="{handleSubmit}">저장하기</button>
    </form>
</div>