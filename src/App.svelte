<script>
  import Router from 'svelte-spa-router';
    import Index from './routes/index.svelte';
    import Error from './routes/error.svelte';
    import Register from './routes/user/register.svelte';
    import Login from './routes/user/login.svelte';
    import Logout from './routes/user/logout.svelte';
    import Profile from './routes/user/profile.svelte';
    import Plans from './routes/plan/plans.svelte';
    import PlanCreate from './routes/plan/planCreate.svelte';
    import PlanCreateCreateDays from './routes/plan/planCreate_createDays.svelte';
    import PlanOverview from './routes/plan/planOverview.svelte';
    import PlanDelete from './routes/plan/planDelete.svelte';
    import PlanNewDays from './routes/plan/planNewDays.svelte';
    import PlanDayOverview from './routes/plan/planDayOverview.svelte';
    import PlanDayDelete from './routes/plan/planDayDelete.svelte';
    import PlanDayAddExercise from './routes/plan/planDay_addExercise.svelte';
    import PlanDayAddExerciseAdditionalData from './routes/plan/planDay_addExercise_additionalData.svelte';
    import PlanDayModifyExercise from './routes/plan/planDay_modifyExercise.svelte';
    import PlanDayExerciseDelete from './routes/plan/planDay_exerciseDelete.svelte';
    import Diet from './routes/diet.svelte';
    import Header from './lib/header.svelte';
    



  const routes = {
    "/" : Index,
    "/404" : Error,
    "/register" : Register,
    "/login" : Login,
    "/logout" : Logout,
    "/profile" : Profile,
    "/plans" : Plans,
    /*"/plans/addNextDay" : AddNextDay,*/
    "/plans/create/plan" : PlanCreate,
    "/plans/create/days" : PlanCreateCreateDays,
    "/plans/:workoutPlanId/overview" : PlanOverview,
    "/plans/:workoutPlanId/delete" : PlanDelete,
    "/plans/:workoutPlanId/overview/newDay" : PlanNewDays,
    "/plans/:workoutPlanId/overview/:workoutDayId" : PlanDayOverview,
    "/plans/:workoutPlanId/overview/:workoutDayId/delete" : PlanDayDelete,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/add" : PlanDayAddExercise,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/add/:exerciseId/info" : PlanDayAddExerciseAdditionalData,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/modify/:exerciseId" : PlanDayModifyExercise,
    "/plans/:workoutPlanId/overview/:workoutDayId/exercise/delete/:exerciseId" : PlanDayExerciseDelete,
    "/diet" : Diet
  }
</script>

<main>
  <Header/>
  <Router {routes}/>
</main>

