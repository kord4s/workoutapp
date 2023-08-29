<script>
    import { onMount } from "svelte";
    import InputNumber from "../../lib/input_number.svelte";
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";

export let params = {}
let workoutPlanID = params.workoutPlanId;
let workoutDayId = params.workoutDayId;
let userExerciseId = params.exerciseId;
let exercise = [];
let data;
let checker=0;
onMount(async function(){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const exerciseResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises/"+userExerciseId,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await exerciseResponse).ok)
    {
        exercise = await(await exerciseResponse).json();
        checker=1;
    }
})



async function tryToAddExercise(e)
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let addingResult = [];
    e.preventDefault()
    const formData = new FormData(e.target);
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    const editingUserExercise = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises/"+userExerciseId+"/editNumbers",
        {
            method: 'PUT',
            body: data,
            headers:{
                'Content-Type' : 'application/json',
                'Authorization' : `${token}`
            },
            credentials: 'include',
            mode: 'cors'              
        })
        if((await editingUserExercise).ok)
        {
            checker=2;
        }
}

</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/plans/{workoutPlanID}/overview/{workoutDayId}'>BACK</a>
    </div>
    {#if (checker == 0)}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
    {:else if (checker==1)}
    <div class='addingExercise'>
        <div class='exerciseContainer'>
            <fieldset>
                <legend>{exercise['exerciseName']}</legend>
                <p>{exercise['description']}</p>
            </fieldset>
        </div>
        <div class='exerciseContainer'>
            <form on:submit|preventDefault={tryToAddExercise}>
                <InputNumber style='' type='number' placeholder='sets' name='NumberOfSeries' min='1' max='' value={exercise['numberOfSeries']}/>
                <InputNumber style='' type='number' placeholder='repetitions' name='NumberOfRepeats' min='1' max='' value={exercise['numberOfRepeats']}/>
                <InputNumber style='' type='number' placeholder='load' name='NumberOfLoad' min='0' max='' value={exercise['numberOfLoad']}/>
                <button>EDIT</button>
            </form>
         </div>
    </div>
    {:else if (checker==2)}
        <h1>SUCCESS, REDIRECTING</h1>
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
         </div>
         <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/{workoutDayId}'"/>
    {/if}
</main>