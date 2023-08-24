<script>
    import { onMount } from "svelte";

    let loginStatus = sessionStorage.getItem("loginStatus");
    let username = sessionStorage.getItem("username");
    let preferredPlan = [];
    let havePreferredPlan=0;
    let waiter;
onMount(async function(){
    if(loginStatus == null)
    {
        waiter=-1;
        return;
    }
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const getPreferedPlanResponse = fetch("https://localhost:7190/api/user/"+userID+"/preferred",
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'  
    })
    if((await getPreferedPlanResponse).ok)
    {
        preferredPlan = await(await getPreferedPlanResponse).json();
        havePreferredPlan = 1;
    }
    else
    {
        havePreferredPlan = -1;
    }
    waiter = 1;
    })

</script>
    

<main>
    <div class="container">
    {#if loginStatus != null && waiter == 1}
        <div class="welcome">
            <h1>WELCOME {username.toUpperCase()}</h1>
        </div>
        {#if havePreferredPlan == 1}
            <a href='/#/plans/{preferredPlan["workoutPlanId"]}/overview'>
                <div class="welcome">       
                    <h1>YOUR PREFERRED PLAN: {preferredPlan['name']}</h1>
                </div>
            </a>
        {:else}
            <a href='/#/plans/'>
                <div class="welcome">       
                    <h1>YOU HAVEN'T PICKED PREFERRED PLAN YET</h1>
                </div>
            </a>
        {/if}

    {:else if waiter == 1}
        <div class="welcome">
            <h1>MAKE YOUR LIFE EASIER WITH HEALTHY LIFESTYLE</h1>
            <h1><a href='/#/register'>REGISTER</a> OR <a href='/#/login'>LOG IN</a></h1>
        </div>
    {:else}

    {/if}
    </div>

</main>