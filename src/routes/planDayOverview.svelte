<script>
    import { onMount } from "svelte";
    import Modal from "../lib/modal.svelte";

export let params = {};
let exercises = [];
let checker = -1;
let workoutPlanID = params.workoutPlanId;
let workoutDayId = params.workoutDayId;
let showModal = false;
let clicked = -1;
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
        checker = 0;
        console.log(exercises);
    }

})

function showModalOnce(exerciseId)
{
    showModal=true;
    clicked = exerciseId;
}


</script>


<main>
{#if (exercises.length == 0 && checker == -1)}
    <div class='container'>
        <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
        <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
        </svg>
    </div>
{:else if (exercises.length == 0)}
    <h1>YOUR WORKOUT DAY IS EMPTY!</h1>
    <h1>IT IS TIME TO ADD SOME EXERCISES</h1>
    <h1><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/add'>CLICK HERE</a></h1>
{:else}
    {#each exercises as exercise}
        <Modal bind:showModal bind:clicked bind:exerciseId={exercise.userExerciseId}>
            <h2>{exercise.exerciseName}</h2>
            <h5>{exercise.description}</h5>
            <hr>
            <h5><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/delete/{exercise.userExerciseId}'>DELETE EXERCISE</a></h5>
            <h5><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/modify/{exercise.userExerciseId}'>MODIFY VALUES</a></h5>
        </Modal>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <h4 on:click={() => (showModalOnce(exercise.userExerciseId))}>{exercise.exerciseName}</h4>
        <h6>sets : {exercise.numberOfSeries}</h6>
        <h6>reps : {exercise.numberOfRepeats}</h6>
        {#if (exercise.numberOfLoad != 0)}
        <h6>load : {exercise.numberOfLoad}</h6>
        {/if}

    {/each}
{/if}

</main>