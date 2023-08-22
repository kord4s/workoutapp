<script>
    import { onMount } from "svelte";
    import Modal from "../lib/modal.svelte";

export let params = {}
let workoutPlanID = params.workoutPlanId;
let checker=0;
let baseDayTemplates = [];
let userSelected
let showModal = false;
let clicked = -1;
let newDayId;
let calendarID;
let date = new Date();
let currentDate = `${date.getDate()}-${date.getMonth()+1}-${date.getFullYear()}`
console.log(currentDate);
onMount(async function()
{
    if(!(await checkIfDayIsAvailable()))
    {
        checker = -2;
        return;
    }
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const baseDayCheckerResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/checkBaseDays",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await baseDayCheckerResponse).ok)
    {
        checker=1;
        var baseDays = await(await baseDayCheckerResponse).json();
        const baseDaysResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/getBaseDays",
        {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
        })
        if((await baseDaysResponse).ok)
        {
            baseDayTemplates = await(await baseDaysResponse).json();
            checker=1;
        }
    }
    else
        checker=-1;
})

function showModalOnce(exerciseId)
{
    showModal=true;
    clicked = exerciseId;
}

async function checkIfDayIsAvailable()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const gettingCalendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/getAllUserCalendars",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'                    
        })
        if((await gettingCalendarResponse).ok)
        {
            let calendarCreationResult = await(await gettingCalendarResponse).json()
            calendarID = calendarCreationResult[0]['calendarId']; 
        }
    const checkingIfAvailableResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/checkIfAvailable/"+currentDate,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'                    
        })
    return (await checkingIfAvailableResponse).json();
}

async function tryToAddNewDay()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let baseDayId = baseDayTemplates[userSelected].workoutDayId;
    let calendarDayID;
    let dataJSON = {calendarDate : currentDate}
    let dataString = JSON.stringify(dataJSON);
    try{
    const creatingCalendarDaysResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/create",
    {
        method: 'POST',
        body: dataString,
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'                    
    })
    if((await creatingCalendarDaysResponse).status)
    {
        calendarDayID = await(await creatingCalendarDaysResponse).json();
    }} catch(e) {
    const gettingCalendarDayIdResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/getByDate/"+currentDate,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'                    
    })
    if((await gettingCalendarDayIdResponse).status)
    {
        calendarDayID = await(await gettingCalendarDayIdResponse).json();
    }
    }

    dataJSON = {calendarDate : currentDate, CalendarDayId : calendarDayID}
    dataString = JSON.stringify(dataJSON);
    
    const addWorkoutDayResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/create/nextDay/"+baseDayId,
        {
        method: 'POST',
        body: dataString,
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
        })
        if((await addWorkoutDayResponse).ok)
        {
            newDayId = await(await addWorkoutDayResponse).json();
            checker=2;
        }
        else
        {
            console.log(await(await addWorkoutDayResponse).json());
        }
}

</script>

<main>
    <h6><a href='/#/plans/{workoutPlanID}/overview'>BACK</a></h6>
    {#if checker == 0}
    <div class='container'>
        <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
        <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
        </svg>
    </div>
    {:else if checker == 1}
        <h4>which day you want to use as a base?</h4>
        <form>
            <fieldset>
                <legend>CHOOSE BODY PART</legend>
                {#each baseDayTemplates as day, index}
                    <input type='radio' value={index} id='{String(index)}' bind:group={userSelected} />
                    <label for='index'>{day.workoutDayId}</label>
                {/each}
            </fieldset>
        </form>

        {#if userSelected!=null}
                {#each baseDayTemplates[userSelected].userExercises as exercise}
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <h4 on:click={() => (showModalOnce(exercise.userExerciseId))}>{exercise.exerciseName}</h4>        
                    <Modal bind:showModal bind:clicked bind:exerciseId={exercise.userExerciseId}>
                        <h2>{exercise.exerciseName}</h2>
                        <h5>{exercise.description}</h5>
                        <hr>
                    </Modal>
                {/each}
                <button on:click={tryToAddNewDay}>ADD</button>
            <h4>YOU MAY ADD OR REMOVE EXERCISES LATER</h4>
        {/if}

    {:else if checker == -1}
        <h1>YOU NEED TO FINISH YOUR BASE DAYS TO</h1>
        <h1>ADD NEW WORKOUT DAYS</h1>
    {:else if checker == -2}
        <h1>TODAYS DATE IS NOT AVAILABLE</h1>
        <h1>MODIFY YOUR LATEST DAY TO KEEP TRACK OF EXERCISING</h1>
    {:else if checker == 2}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/{newDayId}'"/>           
    {/if}
</main>