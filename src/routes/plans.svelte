<script>
    import { onMount } from "svelte";
    let WPCount=-1;
    


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
                let result = await(await response).json()
                WPCount = result;
                //console.log(WPCount);
            }
    }
)
</script>

<main>
    {#if (WPCount == 0)}
    <div>
        <h1>START YOUR FITNESS JOURNEY RIGHT NOW!</h1>
        <h1><a href='/#/plans/create'>CLICK HERE TO CREATE CREATE YOUR PLAN</a></h1>
    </div>
    {:else if (WPCount == 1)}
    <div>
         
    </div>
    <div></div>
    {:else if (WPCount > 1)}
    <div></div>
    {/if}

</main>