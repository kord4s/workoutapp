<script>
    import { onMount } from "svelte";
    export let params = {};
let checker;
let deleted_check=0;
let workoutPlanID = params.workoutPlanId;
let workoutDayId = params.workoutDayId;

onMount(async function(){
    let baseDays = [];
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const testIfBaseResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/getBaseDays",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await testIfBaseResponse).ok)
    {
        baseDays = await(await testIfBaseResponse).json();
        for(let i=0; i<baseDays.length; i++)
        {
            if(baseDays[i]['workoutDayId'] == workoutDayId)
            {
                checker = -1;
                break;
            }
            else
                checker = 1;
        }
    }
})

async function deleteWorkoutDay(){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const deleteWorkoutDayResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/delete",
    {
        method: 'DELETE',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await deleteWorkoutDayResponse).ok)
    {
        checker = 0;
        deleted_check = 1;
    }
    else
    {
        checker = 0;
        deleted_check = -1;
    }
}

</script>

<main>
    <h6><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}'>BACK</a></h6>
    {#if deleted_check == 1}
        <h1>PLAN DAY DELETED</h1>
        <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {:else if deleted_check == -1}
        <h1>PLAN DAY NOT DELETED</h1>
        <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {/if}

    {#if checker == 1}
        <h1>ARE YOU SURE YOU WANT TO DELETE THIS DAY?</h1>
        <h1>IT CANNOT BE RESTORED!</h1>
        <button on:click={deleteWorkoutDay}></button>
    {:else if checker == -1}
        <h1>YOU CANNOT DELETE BASE DAY!</h1>
        <h1>IF YOU WANT SHORTER PLAN, DELETE THIS ONE AND CREATE NEW</h1>
        <meta http-equiv="refresh" content="4; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {:else}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
    {/if}
</main>