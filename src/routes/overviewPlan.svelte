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
    <h6><a href='/#/plans'>BACK</a></h6>
    <h6><a href='/#/plans'>ADD NEXT DAY</a></h6>
    {name}
    {#each workoutdays as days, index}
        <h1><a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">{days.workoutDayId} day {index+1}</a></h1>
    {/each}
</main>