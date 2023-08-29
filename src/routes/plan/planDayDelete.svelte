<script>
    import { onMount } from "svelte";
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
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
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/plans/{workoutPlanID}/overview/{workoutDayId}'>BACK</a>
    </div>
    
    {#if deleted_check == 1}
        <div class="welcome">
        <h1>PLAN DAY DELETED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {:else if deleted_check == -1}
        <div class="welcome">
        <h1>PLAN DAY NOT DELETED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {/if}

    {#if checker == 1}
        <div class="welcome">
        <h1>ARE YOU SURE YOU WANT TO DELETE THIS DAY?</h1>
        <h1>IT CANNOT BE RESTORED!</h1>
        <button on:click|preventDefault={deleteWorkoutDay}>DELETE</button>
        </div>
        
    {:else if checker == -1}
        <div class="welcome">
        <h1>YOU CANNOT DELETE BASE DAY!</h1>
        <h1>IF YOU WANT SHORTER PLAN, DELETE THIS ONE AND CREATE NEW</h1>
        </div>
        <meta http-equiv="refresh" content="1; url='/#/plans/{workoutPlanID}/overview/'"/> 
    {/if}
</main>