<script>
    import { push, link } from 'svelte-spa-router'
    import Error from "../components/Error.svelte"
    import fastapi from "../lib/api"
    import { CollapsibleCard, Accordion, AccordionItem } from 'svelte-collapsible'

    export let params = {}

    let error = {detail:[]}

    let model_uuid = params.model_uuid; // 여기서 model_uuid 값을 설정해야 합니다. 예: 페이지의 URL로부터.
    let histories = [];
    let openIndexes = new Set();
  
    function fetchModelHistory() {
        fastapi('get', `/api/model/${model_uuid}/history`, {}, 
            (response) => {
                histories = response.history;
            },
            (error) => {
                console.error('Failed to fetch model history:', error);
            }
        );
    }
  
    function infernce_download(filename) {
        event.preventDefault();
        fastapi('get', `/api/model/${model_uuid}/${filename}/download`, {}, 
            (response) => {
                console.log('Download successful:', response);
            },
            (error) => {
                console.error('Download failed:', error);
            }
        );
    }

    function model_delete() {
        if(window.confirm('정말로 삭제하시겠습니까?')) {
            let url = `/api/model/${model_uuid}/delete`
            fastapi('delete', url, {}, 
                (json) => {
                    alert("모델 삭제가 완료되었습니다.");
                    push('/dashboard')
                },
                (err_json) => {
                    error = err_json
                }
            )
        }
    }

    let fileInput;
    let name = "";
    function requestSubmit() {
        event.preventDefault();
        let url = `/api/model/${model_uuid}/inference`;

        if (!fileInput.files[0]) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("name", name);
        for(let i = 0; i < fileInput.files.length; i++) {
            formData.append("file", fileInput.files[i]);
        }

        fastapi(
            "post", url, formData,
            (json) => {
                alert("File uploaded successfully!");
            },
            (error) => {
                alert("File upload failed: " + JSON.stringify(error));
            }
        );
        location.reload();
    }

    fetchModelHistory();
</script>
  
<style>
/* 필요한 스타일을 여기에 추가하세요 */

    .history_list {
        border: 3px solid #ccc;
        width: 95%;
        gap: 16px;
        padding: 16px;
        display: flex;
        flex-wrap: wrap;
        margin: 1% auto;
        /* 기타 스타일 */
    }

    .history_item {
        border: 3px solid #ccc;
        width:100%;
        padding: 16px;
        display: flex;
        flex-wrap: wrap;

        /* 기타 스타일 */
    }

    .collapse {
        margin-left: 1%;
        /* 기타 스타일 */
    }

    .inference {
        border: 3px solid #ccc;
        width: 95%;
        gap: 16px;
        padding: 16px;
        display: flex;
        flex-wrap: wrap;
        margin: 1% auto;
        /* 기타 스타일 */
    }

    .delete {
        margin-left: auto;
    }

</style>

<div class="inference">
    <h5 class="my-3 border-bottom pb-2">이미지 업로드</h5>
    <Error error={error} />
    <form method="post" class="my-3">
        <div class="mb-3">
            <label for="name">제목</label>
            <input type="text" bind:value={name} />
        </div>
        <div class="mb-3">
            <label for="content">내용</label>
            <input type="file" bind:this={fileInput}/>
        </div>
        <button class="btn btn-primary" on:click="{requestSubmit}">저장하기</button>
    </form>
    <button class="btn btn-primary delete" on:click={model_delete}>모델 삭제하기</button>
</div>

<div class='history_list'>
    {#each histories as history, index}
        <!-- 헤더. 이를 클릭하면 아래의 div가 토글됩니다. -->
        <div class='history_item'>
            <button type="button" class="btn btn-info" data-bs-toggle="collapse" data-bs-target="#history-{index}">
                Inference Result {index + 1}
            </button>

            <!-- 토글되는 컨텐츠 -->
            <div id="history-{index}" class="collapse">
                <div>Description: {history.description}</div>
                {#if history.pending}
                    <div>File : Pending...</div>
                {:else}
                    File : <a use:link href="/model/{model_uuid}/history" on:click|preventDefault={() => infernce_download(history.file_name)}> {history.file_name}</a>
                {/if}
            </div>
        </div>
    {/each}
</div>
  