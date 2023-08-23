<script>
    import { onMount } from "svelte";
    let WPCount=-1;
    let WorkoutPlans=[];
    let waiter = 0;
    let checker;

onMount(async function()
    {
        let userID = sessionStorage.getItem("userID");
        console.log(userID);
        console.log(sessionStorage.getItem('loginStatus'));
        if(sessionStorage.getItem("userID") === null)
        {
            checker = -1;
            return;
        }
        checker = 1;
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
                waiter = 1;
            }

        }
    }
)

</script>

<main>
    {#if checker == 1}
        <div class='plans'>
            {#if (WPCount == -1)}
                <div class='container'>
                    <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
                        <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
                    </svg>
                </div>
            {:else if (WPCount == 0)}
                <a href='/#/plans/create/plan'>
                    <h4>START YOUR FITNESS JOURNEY RIGHT NOW!</h4>
                    <h4>CLICK HERE TO CREATE CREATE YOUR PLAN</h4>
                </a>
            {:else}
                {#if waiter == 1}
                    {#each WorkoutPlans as plan, index}
                        <a href='/#/plans/{plan.workoutPlanId}/overview'>{plan.name}</a>
                    {/each}
                        <a href='/#/plans/create/plan'>CLICK HERE TO CREATE CREATE NEW PLAN</a>
                {/if}
            {/if}
        </div>
    {:else}
        <div class="welcome">
            <h1>TO PROPERLY USE OUR WEBSITE</h1>
            <h1>YOU NEED TO SIGN UP OR LOG IN</h1>
            <h1><a href=/#/register>REGISTER</a> OR <a href='/#/login'>LOGIN</a></h1>
        </div>
    {/if}
</main>