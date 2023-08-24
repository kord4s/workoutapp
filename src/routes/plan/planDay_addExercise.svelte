<script>
    import { onMount } from "svelte";
    import Modal from "../../lib/modal.svelte";
    

export let params = {};
let workoutPlanID = params.workoutPlanId;
let bodyparts = [], exercises = [];
let workoutDayId = params.workoutDayId;
let userSelected;
let checker = -1;
let showModal = false;
let clicked = -1;
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

function showModalOnce(exerciseId)
{
    showModal=true;
    clicked = exerciseId;
}

</script>

<main>
    <div class='navigator'>
        <a class='narrowest' href='/#/plans/{workoutPlanID}/overview/{workoutDayId}'>BACK</a>
    </div>
    <div class='addingExercise'>
        <div class='exerciseContainer'>
            <form>
                <fieldset>
                    <legend>CHOOSE BODY PART</legend>
                        {#each bodyparts as part, index}
                            <div class='radio'>
                                <input type='radio' value='{part}' id='{String(index)}' bind:group={userSelected} on:change={searchForExercises}/>
                                <label for='{String(index)}'>{part}</label>
                            </div>
                        {/each}
                </fieldset>
            </form>
        </div>
        <div class='exerciseContainer'>
            {#if checker==1}
                {#each exercises as exercise}
                    <div class='exerciseToAdd' on:click={() => (showModalOnce(exercise.exerciseId))}>
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <p >{exercise.exerciseName}<p>
                    <Modal bind:showModal bind:clicked bind:exerciseId={exercise.exerciseId}>
                        <h2>{exercise.exerciseName}</h2>
                        <h5>{exercise.description}</h5>
                        <div class='modalNavigator'>
                            <a class='narrowest' href='#/plans/{workoutPlanID}/overview/{workoutDayId}/exercise/add/{exercise.exerciseId}/info'>ADD TO MY WORKOUT PLAN</a>
                        </div>
                    </Modal>
                    </div>
                {/each}
            {/if}
        </div>
    </div>


</main>