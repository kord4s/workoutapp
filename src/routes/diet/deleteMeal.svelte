<script>
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";

export let params = {}
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let deleted_check=0;
async function deleteMeal()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const deleteMealResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/delete`,
    {
        method: 'DELETE',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await deleteMealResponse).ok)
    {
        deleted_check = 1;
    }
    else
    {
        deleted_check = -1;
    }
}

</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'>BACK</a>
    </div>
    
    {#if deleted_check == 1}
        <div class="welcome">
        <h1>MEAL DELETED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'"/> 
    {:else if deleted_check == -1}
        <div class="welcome">
        <h1>MEAL NOT DELETED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'"/> 
    {:else if deleted_check  == 0}
        <div class="welcome">
        <h1>ARE YOU SURE YOU WANT TO DELETE THIS MEAL?</h1>
        <h1>IT CANNOT BE RESTORED!</h1>
        <button on:click|preventDefault={deleteMeal}>DELETE</button>
        </div>
    {/if}
</main>