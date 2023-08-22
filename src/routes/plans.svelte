<script>
    import { onMount } from "svelte";
    let WPCount=-1;
    let WorkoutPlans=[];


onMount(async function()
    {
        let userID = sessionStorage.getItem("userID");
        let token = "Bearer "+sessionStorage.getItem("token");
        
        const response = fetch('https://localhost:7190/api/'+userID+'/workoutplans/count',
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'                
            }
        )
        if((await response).ok)
        {
            let result = await(await response).json();
            WPCount = result;
            console.log(WPCount);
        }
        if(WPCount>0)
        {
            const workoutPlansResponse = fetch('https://localhost:7190/api/'+userID+'/workoutplans',
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors' 
            })
            if((await workoutPlansResponse).ok)
            {
                WorkoutPlans = await(await workoutPlansResponse).json();
                console.log(WorkoutPlans);
            }

        }
    }
)

</script>

<main>

    {#if (WPCount == -1)}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
    {:else if (WPCount == 0)}
    <div>
        <h1>START YOUR FITNESS JOURNEY RIGHT NOW!</h1>
        <h1><a href='/#/plans/create/plan'>CLICK HERE TO CREATE CREATE YOUR PLAN</a></h1>
    </div>
    {:else}
    <div>
        {#each WorkoutPlans as plan, index}
        <h1><a href='/#/plans/{plan.workoutPlanId}/overview'>{plan.name}</a></h1>
     {/each}
    </div>
    {/if}

</main>