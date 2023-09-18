<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import _Home from "./routes/Home copy.svelte"
  import Inference from "./routes/Inference.svelte"
  import Detail from "./routes/Detail.svelte"
  import QuestionCreate from "./routes/QuestionCreate.svelte"
  import Navigation from "./components/Navigation.svelte"
  import UserCreate from "./routes/UserCreate.svelte"
  import UserLogin from "./routes/UserLogin.svelte"
  import QuestionModify from "./routes/QuestionModify.svelte"
  import UploadModel from './routes/UploadModel.svelte'

  const routes = {
    '/': _Home,
    '/detail/:question_id': Detail,
    '/question-create': QuestionCreate,
    '/user-create': UserCreate,
    '/user-login': UserLogin,
    '/question-modify/:question_id': QuestionModify,
    '/upload-model': UploadModel,
    '/inference/:model_uuid': Inference
  }
</script>

<Navigation />
<Router {routes}/>
