<script>
  import Router from 'svelte-spa-router'
  import Home from "./routes/Home.svelte"
  import Inference from "./routes/Inference.svelte"
  import Navigation from "./components/Navigation.svelte"
  import UserCreate from "./routes/UserCreate.svelte"
  import UserLogin from "./routes/UserLogin.svelte"
  import UploadModel from './routes/UploadModel.svelte'
  import Dashboard from './routes/Dashboard.svelte'
  import ModelHistory from './routes/ModelHistory.svelte'

  const routes = {
    '/': Dashboard,
    '/user-create': UserCreate,
    '/user-login': UserLogin,
    '/model/upload': UploadModel,
    '/model/:model_uuid/history': ModelHistory,
    '/dashboard': Dashboard,
  }
</script>

<Navigation />
<Router {routes}/>
