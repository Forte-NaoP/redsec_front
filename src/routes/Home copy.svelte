<script>
    import fastapi from "../lib/api"
    import { link, push } from 'svelte-spa-router'
    import { page, is_login } from "../lib/store"

    let model_list = []
    let size = 10
    let total = 0
    $: total_page = Math.ceil(total/size) // 스벨트에서 변수앞에 $: 기호를 붙이면 해당 변수는 반응형 변수가 된다.

    function get_model_list(_page) {
        let params = {
            page: _page,
            size: size,
        }
        fastapi('get', '/api/model/list', params, (json) => {
            model_list = json.models
            $page = _page // 스토어 변수는 $page 처럼 변수명 앞에 $ 기호를 덧붙여 참조해야 한다
            total = json.total
        })
    }

    get_model_list($page);

</script>

<div class="container my-3">
    <table class="table">
        <thead>
            <tr class="text-center table-dark">
                <th>번호</th>
                <th style="width:50%">제목</th>
            </tr>
        </thead>
        <tbody>
        {#each model_list as model, i}
        <tr class="text-center">
            <td>{ total - ($page * size) - i }</td>
            <td class="text-start">
                <a use:link href="/inference/{model.ML_model_uuid}">{model.ML_model_name}</a>
            </td>
        </tr>
        {/each}
        </tbody>
    </table>
    <!-- 페이징처리 시작 -->
    <ul class="pagination justify-content-center">
        <!-- 이전페이지 -->
        <li class="page-item {$page <= 0 && 'disabled'}">
            <button class="page-link" on:click="{() => get_model_list($page-1)}">이전</button>
        </li>
        <!-- 페이지번호 -->
        {#each Array(total_page) as _, loop_page}
        {#if loop_page >= $page-5 && loop_page <= $page+5} 
        <li class="page-item {loop_page === $page && 'active'}">
            <button on:click="{() => get_model_list(loop_page)}" class="page-link">{loop_page+1}</button>
        </li>
        {/if}
        {/each}
        <!-- 다음페이지 -->
        <li class="page-item {$page >= total_page-1 && 'disabled'}">
            <button class="page-link" on:click="{() => get_model_list($page+1)}">다음</button>
        </li>
    </ul>
    <!-- 페이징처리 끝 -->
    <a use:link href="/upload-model" 
        class="btn btn-primary {$is_login ? '' : 'disabled'}">모델 업로드하기</a>
</div>
