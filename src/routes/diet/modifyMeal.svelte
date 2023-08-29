<script>
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
import Input from "../../lib/input.svelte";
    export let params = {}
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let modify_check=0;
async function modifyMeal(e)
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let data;
    e.preventDefault();
    const formData = new FormData(e.target);
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    const updateMealResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/edit`,
    {
        body: data,
        method: 'PUT',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await updateMealResponse).ok)
    {
        modify_check = 1;
    }
    else
    {
        modify_check = -1;
    }
}
</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'>BACK</a>
    </div>
    
    {#if modify_check == 1}
        <div class="welcome">
        <h1>MEAL MODIFIED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'"/> 
    {:else if modify_check == -1}
        <div class="welcome">
        <h1>MEAL NOT MODIFIED</h1>
        </div>
        <meta http-equiv="refresh" content="2; url='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'"/> 
    {:else if modify_check  == 0}
        <div class='plans'>
            <form on:submit|preventDefault={modifyMeal}>
                <Input name="MealName" type="text" placeholder="new meal name" style=''/>
                <button>ADD NEW MEAL</button>
            </form>
        </div>
    {/if}
</main>