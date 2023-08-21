<script>
    import { onMount } from "svelte";
    import Modal from "../lib/modal.svelte";
    

export let params = {};
let workoutPlanID = params.workoutPlanId;
let bodyparts = [], exercises = [];
let workoutDayId = params.workoutDayId;
let userSelected;
let checker = -1;
let showModal = false;
onMount(async function(){
    
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    //getting bodyparts to make a select input
    const bodypartsResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises/getsamples/bodyparts",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await bodypartsResponse).ok)
    {
        bodyparts = await(await bodypartsResponse).json();
    }
})

async function searchForExercises()
{
    if(userSelected == null)
        return;
    checker=-1;
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const exercisesResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises/getsamples/bybodypart/"+userSelected,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await exercisesResponse).ok)
    {
        exercises = await(await exercisesResponse).json();
        checker=1;
    }
}
</script>

<main>
    <form>
        <fieldset>
            <legend>CHOOSE BODY PART</legend>
            {#each bodyparts as part, index}
                <input type='radio' value='{part}' id='{String(index)}' bind:group={userSelected} on:change={searchForExercises}/>
                <label for='index'>{part}</label>
            {/each}
        </fieldset>
    </form>

    {#if checker==1}
        {#each exercises as exercise}
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <h4 on:click={() => (showModal = true)}>{exercise.exerciseName}</h4>
            <Modal bind:showModal exerciseId>
                <h2>{exercise.exerciseName}</h2>
                <h5>{exercise.description}</h5>
                <hr>
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <h4><a href='#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/add/{exercise.exerciseId}/info'>ADD TO MY WORKOUT PLAN</a></h4>
            </Modal>
        {/each}
    {/if}




</main>