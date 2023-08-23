<script>
    let checker = -1;
    export let params = {}
    let workoutPlanID = params.workoutPlanId;
    let workoutDayId = params.workoutDayId;
    let userExerciseId = params.exerciseId;
    async function deleteUserExercise()
    {
        checker = 1;
        let token = 'Bearer '+sessionStorage.getItem("token");
        let userID = sessionStorage.getItem("userID");
        const deletingResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/"+workoutDayId+"/exercises/"+userExerciseId+"/delete",
        {
            method: 'POST',
            headers:{
                'Content-Type' : 'application/json',
                'Authorization' : `${token}`
            },
            credentials: 'include',
            mode: 'cors'              
        })
        if((await deletingResponse).ok)
        {
            checker = 0;
        }
    }
</script>

<main>
    <h6><a href='/#/plans/{workoutPlanID}/overview/{workoutDayId}'>BACK</a></h6>
    {#if checker == -1}
        <h1> ARE YOU SURE?</h1>
        <button on:click = {deleteUserExercise}></button>
    {:else}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
         </div>
         <meta http-equiv="refresh" content="2; url='/#/plans/{workoutPlanID}/overview/{workoutDayId}'"/>
    {/if}
</main>