<script>
    import { onMount } from "svelte";

export let params = {};
let exercises = [];
let workoutPlanID = params.workoutPlanId;
let workoutDayId = params.workoutDayId;
onMount(async function()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const workoutDayExercisesResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await workoutDayExercisesResponse).ok)
    {
        exercises = await(await workoutDayExercisesResponse).json();
    }

})

function redirect()
{
    window.location.href = "/#/plans/overview/"+workoutDayId+"/exercise/add";
}

</script>


<main>
{#if (exercises.length == 0)}
    <h1>YOUR WORKOUT DAY IS EMPTY!</h1>
    <h1>IT IS TIME TO ADD SOME EXERCISES</h1>
    <h1><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/add'>CLICK HERE</a></h1>
{:else}
    cwiczenia + dodaj cwiczenie + usun cwiczenie
{/if}

</main>