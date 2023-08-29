<script>
    import { onMount } from "svelte";
    import Input from "../../lib/input.svelte";
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
    export let params = {};
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let checker;
async function createMeal(e){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let data;
    e.preventDefault();
    const formData = new FormData(e.target);
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    const addMealResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/create`,
    {
        body: data,
        method: 'POST',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'
    })
    if((await addMealResponse).ok)
    {
        checker = 1;
    }
}
</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/overview'>BACK</a>
    </div>
    <div class='plans'>
        <form on:submit|preventDefault={createMeal}>
            <Input name="MealName" type="text" placeholder="meal name" style=''/>
            <button>ADD NEW MEAL</button>
            {#if checker == 1}
                {history.back()}
            {/if}
        </form>
    </div>
</main>