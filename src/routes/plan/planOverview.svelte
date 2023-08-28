<script>
import { onMount } from "svelte";
export let params = {}
let workoutPlanID = params.workoutPlanId;
let workoutdays=[];
let waiter = 0;
let incrementDays=0;
let daysCount;
let isPrefered;

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

    const workoutPlanResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await workoutPlanResponse).ok)
    {
        let workoutPlan = await(await workoutPlanResponse).json();
        daysCount = workoutPlan['daysCount'];
    }

    const preferedPlanResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/isPrefered/"+workoutPlanID,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'  
    })
    if((await preferedPlanResponse).ok)
    {
        if(await(await preferedPlanResponse).json())
        {
            isPrefered=true;
        }
        else
        {
            isPrefered=false;
        }
    }
})

async function setAsPrefered()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const setAsPreferedPlanResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/setAsPrefered/"+workoutPlanID,
    {
        method: 'PUT',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'  
    })
    if((await setAsPreferedPlanResponse).ok)
    {
        location.reload();
    }
}

async function unsetAsPrefered()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const setAsNotPreferedPlanResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/notPrefered/"+workoutPlanID,
    {
        method: 'PUT',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'  
    })
    if((await setAsNotPreferedPlanResponse).ok)
    {
        location.reload();
    }
}

function nextDays()
{
    if(incrementDays*10+daysCount < workoutdays.slice(daysCount,incrementDays*10+daysCount))
        incrementDays++;
}

function previousDays()
{
    if(incrementDays>=0)
    {
        incrementDays--;
    }
}

</script>

<main>
    {#if isPrefered == true || isPrefered == false}
        <div class='navigator'>
        <a href='/#/plans' class='wider'>BACK</a>
        <a href='/#/plans/{workoutPlanID}/overview/newDay' class='wider'>ADD NEXT DAY</a>
        <a href='/#/plans/{workoutPlanID}/delete' class='wider'>DELETE</a>
        {#if isPrefered==true}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
            <p on:click={unsetAsPrefered} class='wider'>UNSET AS PREFERED</p>
        {:else if isPrefered==false}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
            <p on:click={setAsPrefered} class='wider'>SET AS PREFERED</p>
        {/if}
        </div>
        {#if workoutdays.length == daysCount}
            <div class="plans">
                {#if waiter == 1}
                    {#each workoutdays as days, index}
                        <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">BASE DAY {index+1}</a>
                    {/each}
                {/if}
            </div>
        {:else}
            <div class='adddingExercise'>
                <div class='planOverview'>
                    <p class='exerciseToAdd'>BASE DAYS</p>
                    {#each workoutdays.slice(0,daysCount) as days, index}
                        <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">BASE DAY {index+1}</a>                
                    {/each}
                </div>
                <div class='planOverview'>
                        <p class='exerciseToAdd'>TRACKING DAYS</p>
                    {#if (workoutdays.slice(daysCount, workoutdays.slice.length).length < 10)}
                        {#each workoutdays.slice(daysCount, workoutdays.length).reverse() as days, index}
                            <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">DAY {workoutdays.length - daysCount - index}</a>  
                        {/each}
                    {:else}
                        <div on:click={previousDays} class='exerciseToAdd narrowDay'>-10</div>
                        <div on:click={nextDays} class='exerciseToAdd narrowDay'>+10</div>
                        {#each workoutdays.slice(daysCount+incrementDays*10, daysCount+10+incrementDays*10).reverse() as days, index}
                            <a href="/#/plans/{workoutPlanID}/overview/{days.workoutDayId}">DAY {workoutdays.length - daysCount - index - incrementDays*10}</a>  
                        {/each}
                    {/if}
                </div>
            </div>
        {/if}
    {/if}
</main>