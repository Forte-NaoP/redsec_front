<script>
    import { push } from 'svelte-spa-router'
    import fastapi from "../lib/api"
    import Error from "../components/Error.svelte"

    let error = {detail:[]}
    let model, weight, parms, galois_key, relin_key, pub_key;
    let fileInputs = {"model": model, "weight": weight, "ckks_parms": parms, "galois_key": galois_key, "relin_key": relin_key, "pub_key": pub_key};
    let name = "";
    let files = {};

    function handleFileChange(event, key) {
        files[key] = event.target.files[0];
    }

    function handleSubmit() {
        event.preventDefault()
        let url = "/api/model/upload"

        if (name === "") {
            alert("제목을 입력해 주세요.");
            return;
        }

        let allFilesSelected = Object.keys(fileInputs).every(key => files[key] && files[key].size > 0);
        if (!allFilesSelected) {
            alert("모든 파일을 선택해 주세요.");
            return;
        }

        const formData = new FormData();
        formData.append("name", name);
        Object.keys(files).forEach(key => {
            formData.append(key, files[key]);
        });

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
            <label for="name">모델 이름: </label>
            <input type="text" bind:value={name} />
        </div>

        {#each Object.entries(fileInputs) as [key, value]}
            <div class="mb-3">
                <label for="{key}">{key}: </label>
                <input type="file" id="{key}" on:change="{event => handleFileChange(event, key)}" />
            </div>
        {/each}

        <button class="btn btn-primary" on:click="{handleSubmit}">저장하기</button>
    </form>
</div>