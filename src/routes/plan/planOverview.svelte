<script>
import { onMount } from "svelte";
export let params = {}
let workoutPlanID = params.workoutPlanId;
let workoutdays=[];
let name = sessionStorage.getItem("Name");
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
        console.log(workoutdays);
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
    {#each workoutdays as days, index}
        <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">{days.workoutDayId} day {index+1}</a>
    {/each}
    </div>
</main>