<script>
    import { onMount } from "svelte";
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
let checker;
let calendarID;
let date = new Date();
let currentDate = `${date.getDate()}-${date.getMonth()+1}-${date.getFullYear()}`
//date manipulation
date.setDate(date.getDate()-1);
let yesterday = {day : date.getDate(), month : date.getMonth(), year : date.getFullYear() };
date.setDate(date.getDate()+2);
let tomorrow = {day : date.getDate(), month : date.getMonth(), year : date.getFullYear() };
date.setDate(date.getDate()-1);
let calendarDayID;
let meals = []
let waiter
onMount(async function(){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let dataJSON = {calendarDate : currentDate}
    let dataString = JSON.stringify(dataJSON);
        if(sessionStorage.getItem("userID") === null)
        {
            checker = -1;
            return;
        }
        checker = 1;
        const calendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/count",
        {
            method: 'GET',
            headers:{
                'Content-Type' : 'application/json',
                'Authorization' : `${token}`
            },
            credentials: 'include',
            mode: 'cors'
        })
        if((await calendarResponse).ok)
        {
            let calendarResult = await(await calendarResponse).json();
            if(calendarResult!=0)
            {
                const gettingCalendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/getAllUserCalendars",
                {
                    method: 'GET',
                    headers:{
                        'Content-Type' : 'application/json',
                        'Authorization' : `${token}`
                    },
                    credentials: 'include',
                    mode: 'cors'                    
                })
                if((await gettingCalendarResponse).ok)
                {
                    let calendarCreationResult = await(await gettingCalendarResponse).json()
                    calendarID = calendarCreationResult[0]['calendarId'];
                }
            }
            else
            {
                const calendarCreationResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/create",
                {
                    body: '{}',
                    method: 'POST',
                    headers:{
                        'Content-Type' : 'application/json',
                        'Authorization' : `${token}`
                    },
                    credentials: 'include',
                    mode: 'cors'                    
                })
                if((await calendarCreationResponse).ok)
                {
                    let calendarCreationResult = await(await calendarCreationResponse).json()
                    calendarID = calendarCreationResult;  
        }}}
        try{
            const creatingCalendarDaysResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/create",
            {
                method: 'POST',
                body: dataString,
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'                    
            })
            if((await creatingCalendarDaysResponse).status)
            {
                calendarDayID = await(await creatingCalendarDaysResponse).json();
            }} 
        catch(e) {
            const gettingCalendarDayIdResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/getByDate/"+currentDate,
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'                    
            })
            if((await gettingCalendarDayIdResponse).status)
            {
                calendarDayID = await(await gettingCalendarDayIdResponse).json();
            }
        }
        const gettingMealsResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/"+calendarDayID+"/meals",
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'                    
            })
        if((await gettingMealsResponse).status)
        {
            meals = await(await gettingMealsResponse).json();
            waiter = 1;
        }
})


</script>

<main>
    <div class='navigator'>
        <a href="/#/diet/overview/{yesterday['day']}/{yesterday['month']+1}/{yesterday['year']}">PREVIOUS DAY</a>
        <a href='/#/diet/{calendarID}/{calendarDayID}/addMeal'>ADD NEXT MEAL</a>
        <a href="/#/diet/overview/{tomorrow['day']}/{tomorrow['month']+1}/{tomorrow['year']}">NEXT DAY</a>
    </div>
    {#if checker == 1}
    {#if waiter == 1 && meals.length > 0}
        <div class='meals'>
        {#each meals as meal}
            <div>
                <p class='title'>{meal.mealName}</p>
                <div class='subtitle'>
                    <p>protein: {Number(meal.totalProtein).toFixed(2)}g</p>
                    <p>fat: {Number(meal.totalFat).toFixed(2)}g</p>
                    <p>carbs: {Number(meal.totalCarbs).toFixed(2)}g</p>
                    <p>energy: {Number(meal.totalKcal).toFixed(0)}kcal</p>
                </div>
                {#each meal.products as product}
                    <a class='product' href='/#/diet/{calendarID}/{calendarDayID}/{meal.mealId}/{product.productId}/modify'>{product.productName}:{product.productWeight}g:{(product.productWeight*(product.productKcal/100)).toFixed(0)}kcal P:{(product.productWeight*(product.productProtein/100)).toFixed(2)}g F:{(product.productWeight*(product.productFat/100)).toFixed(2)}g C:{(product.productWeight*(product.productCarbs/100)).toFixed(2)}g</a>
                {/each}
                <div class='modalNavigator'>
                    <a class='evenMoreNarrower' href='/#/diet/{calendarID}/{calendarDayID}/{meal.mealId}/addProduct'>ADD PRODUCT</a>
                    <a class='evenMoreNarrower' href='/#/diet/{calendarID}/{calendarDayID}/{meal.mealId}/modifyMeal'>MODIFY NAME</a>
                    <a class='evenMoreNarrower' href='/#/diet/{calendarID}/{calendarDayID}/{meal.mealId}/deleteMeal'>DELETE MEAL</a>
                </div>
            </div>
        {/each}
        </div>
    {:else if waiter == 1 && meals.length == 0}
        <div class="welcome">
            <h1>DAY IS EMPTY</h1>
            <h1>ADD SOME MEALS</h1>
        </div>
    {/if}
{:else if checker == -1}
    <div class="welcome">
        <h1>TO PROPERLY USE OUR WEBSITE</h1>
        <h1>YOU NEED TO SIGN UP OR LOG IN</h1>
        <h1><a href='/#/register>REGISTER'>REGISTER</a> OR <a href='/#/login'>LOGIN</a></h1>
    </div>
{:else}
    <div class='container'>
        <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
        </svg>
    </div>
{/if}
</main>