<script>
  import Router from 'svelte-spa-router';
  import Index from './routes/index.svelte';
  import Register from './routes/register.svelte';
  import Login from './routes/login.svelte';
  import Error from './routes/error.svelte';
    import Header from './lib/header.svelte';
    import Logout from './routes/logout.svelte';
    import Profile from './routes/profile.svelte';
    import Plans from './routes/plans.svelte';

  const routes = {
    "/" : Index,
    "/404" : Error,
    "/register" : Register,
    "/login" : Login,
    "/logout" : Logout,
    "/profile" : Profile,
    "/plans" : Plans
  }
</script>

<main>
  <Header/>
  <Router {routes}/>
</main>

