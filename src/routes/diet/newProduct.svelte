<script>
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
import Input from "../../lib/input.svelte";
    import InputNumber from "../../lib/input_number.svelte";
    import InputRealnumber from "../../lib/input_realnumber.svelte";

export let params = {}
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let categoryName = params.categoryName;
let checker;
let addingResult = [];
async function createNewProduct(e)
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    
    e.preventDefault()
    let data;
    const formData = new FormData(e.target);
    formData.append("ProductCategoryName", categoryName);
    formData.append("ProductWeight", "0");
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    console.log(data);
    const addingNewProduct = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/create/template`,
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
    if((await addingNewProduct).ok)
    {
        addingResult = await(await addingNewProduct).json();
        checker=1;
    }
}
</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct'>BACK</a>
    </div>
    {#if checker!=1}
        <div class='addingNewProduct'>
            <form on:submit|preventDefault={createNewProduct}>
            <Input style='' name='ProductName' placeholder='product name' type='text'/>
            <InputRealnumber style='' name='ProductKcal' placeholder='kcal per 100g' type='number' min='0' max='900' value="" step='1'/>
            <InputRealnumber style='' name='ProductCarbs' placeholder='carbs per 100g' type='number' min='0' max='100' value="" step='0.01'/>
            <InputRealnumber style='' name='ProductFat' placeholder='fat per 100g' type='number' min='0' max='100' value="" step='0.01'/>
            <InputRealnumber style='' name='ProductProtein' placeholder='protein per 100g' type='number' min='0' max='100' value="" step='0.01'/>
            <button>ADD NEW PRODUCT</button>
            </form>
        </div>
    {:else}
        <meta http-equiv="refresh" content="2; url='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct/{addingResult['productId']}/additionalData'"/>
    {/if}
</main>