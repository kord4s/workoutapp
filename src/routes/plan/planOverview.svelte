<script>
import { onMount } from "svelte";
export let params = {}
let workoutPlanID = params.workoutPlanId;
let workoutdays=[];
let waiter = 0;
onMount(async function(){

    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const workoutDaysResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await workoutDaysResponse).ok)
    {
        workoutdays = await(await workoutDaysResponse).json();
        waiter = 1;
    }
    
    
})
</script>

<main>
    <div class='navigator'>
    <a href='/#/plans'>BACK</a>
    <a href='/#/plans/{workoutPlanID}/overview/newDay'>ADD NEXT DAY</a>
    <a href='/#/plans/{workoutPlanID}/delete'>DELETE</a>
    </div>
    <div class="plans">
        {#if waiter == 1}
            {#each workoutdays as days, index}
                <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">DAY {index+1}</a>
            {/each}
        {/if}
    </div>
</main>