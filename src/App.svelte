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
    import Diet from './routes/diet.svelte';
    import CreatePlan from './routes/createPlan.svelte';
    import overviewPlan from './routes/overviewPlan.svelte';
    import CreateDays from './routes/CreateDays.svelte';
    import PlanDayOverview from './routes/planDayOverview.svelte';
    import AddingExercise from './routes/addingExercise.svelte';
    import AddingExerciseAdditionaldata from './routes/addingExercise_additionaldata.svelte';

  const routes = {
    "/" : Index,
    "/404" : Error,
    "/register" : Register,
    "/login" : Login,
    "/logout" : Logout,
    "/profile" : Profile,
    "/plans" : Plans,
    "/plans/:workoutPlanId/create/plan" : CreatePlan,
    "/plans/:workoutPlanId/create/days" : CreateDays,
    "/plans/:workoutPlanId/overview" : overviewPlan,
    "/plans/:workoutPlanId/overview/:workoutDayId" : PlanDayOverview,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/add" : AddingExercise,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/add/:exerciseId/info" : AddingExerciseAdditionaldata,
    "/diet" : Diet
  }
</script>

<main>
  <Header/>
  <Router {routes}/>
</main>

