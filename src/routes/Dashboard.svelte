<script>
    import { onMount } from "svelte";
    import { link, push } from 'svelte-spa-router'
    import fastapi from "../lib/api"
  
    let models = [];
  
    onMount(() => {
      fastapi(
        'get', 
        '/api/model/list', 
        {}, 
        data => {
          models = data.models;
        },
        error => {
          console.error(error);
        }
      );
    });
  
    function navigateToHistory(model_uuid) {
      push(`/model/${model_uuid}/history`);
    }

    function navigateToUpload() {
        push('/model/upload');
    }

</script>
  
<style>
    .dashboard {
        display: flex;
        flex-wrap: wrap;
        grid-template-columns: repeat(4, 1fr);
        width: 95%;
        gap: 16px;
        padding: 16px;
        border: 2px solid black;
        margin: 1% auto;
    }

    .item, .upload-button {
        border-radius: 8px;  
        background-color: #ccc;
        padding: 10px;
        margin: 5px;
        display: flex;           /* flexbox 사용 */
        align-items: center;     /* 수직 중앙 정렬 */
        justify-content: center; /* 수평 중앙 정렬 */
    }
    .item, .upload-button {                   
        width: 150px;                /* 고정된 너비 설정 */
        height: 150px;               /* 고정된 높이 설정 */
        text-overflow: ellipsis;     /* 긴 텍스트는 ...로 표시 */
        white-space: nowrap;         /* 텍스트 줄바꿈 방지 */
        overflow: hidden;            /* 숨겨진 내용은 표시하지 않음 */
    }

    .upload-button {
        cursor: pointer;
    }
</style>
  
<div class="dashboard">
    {#each models as model (model.ML_model_uuid)}
        <div class="item" on:click={() => navigateToHistory(model.ML_model_uuid)}>
        {model.ML_model_name}
        </div>
    {/each}

    <div class="upload-button" on:click={navigateToUpload}>
        +
    </div>

</div>