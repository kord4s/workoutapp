<script>
    export let params = {}
    let workoutPlanID = params.workoutPlanId;
    let deleted_check = 0;
    let checker = 1;

    async function deleteWorkoutPlan()
    {
        let token = 'Bearer '+sessionStorage.getItem("token");
        let userID = sessionStorage.getItem("userID");
        const deletePlanResponse = fetch(`https://localhost:7190/api/${userID}/workoutplans/${workoutPlanID}/delete`,{
            method: "DELETE",
            headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
            },
            credentials: 'include',
            mode: 'cors'   
        })
        if((await deletePlanResponse).ok)
        {
            deleted_check=1;
        }
        else
        {
            deleted_check=-1;
        }
        checker=0;
    }
</script>

<main>
    <h6><a href='/#/plans/'>BACK</a></h6>
    {#if deleted_check == 1}
        <h1>PLAN DELETED</h1>
        <meta http-equiv="refresh" content="2; url='/#/plans/'"/> 
    {:else if deleted_check == -1}
        <h1>PLAN DELETED</h1>
        <meta http-equiv="refresh" content="2; url='/#/plans/'"/> 
    {/if}

    {#if checker == 1}
        <h1>ARE YOU SURE YOU WANT TO DELETE THIS PLAN?</h1>
        <h1>IT WILL DESTROY ALSO ALL YOUR TRACKED DAYS</h1>
        <h1>IT CANNOT BE RESTORED!</h1>
        <button on:click={deleteWorkoutPlan}></button>
    {:else}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
    {/if}
</main>