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
    <div class='navigator'>
        <a href='/#/plans/'>BACK</a>
    </div>
    {#if deleted_check == 1}
        <div class='welcome'>
        <h1>PLAN DELETED</h1>
        <h1>REDIRECTING</h1>
        </div>
        <meta http-equiv="refresh" content="1; url='/#/plans/'"/> 
        {:else if deleted_check == -1}
        <div class='welcome'>
        <h1>PLAN DELETED</h1>
        <h1>REDIRECTING</h1>
        </div> 
        <meta http-equiv="refresh" content="1; url='/#/plans/'"/>
    {/if}

    {#if checker == 1}
        <div class='welcome'>
            <h1>ARE YOU SURE YOU WANT TO DELETE THIS PLAN?</h1>
            <h1>IT WILL DESTROY ALSO ALL YOUR TRACKED DAYS</h1>
            <h1>IT CANNOT BE RESTORED!</h1>
            <button on:click|preventDefault={deleteWorkoutPlan}>DELETE</button>
        </div>
    {/if}
</main>