<script>
    export let params = {}
    import { onMount } from "svelte";
    
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let productID = params.productId;
let product = [];
let weight;
$: kcal = weight * (product['productKcal']/100);
$: protein = weight * (product['productProtein']/100);
$: carbs = weight * (product['productCarbs']/100);
$: fat = weight * (product['productFat']/100);
$: if(isNaN(kcal))
{
    kcal = 0;
    protein = 0;
    carbs = 0;
    fat = 0;
}
let checker = -1;
onMount(async function(){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const productResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/${productID}`,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await productResponse).ok)
    {
        product = await(await productResponse).json();
        checker = 1;
    }
})

async function addProductToMeal()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let data;
    const formData = new FormData();
    formData.append("ProductName", product['productName']);
    formData.append("ProductKcal", String(kcal));
    formData.append("ProductWeight", String(weight));
    formData.append("ProductCarbs", String(carbs));
    formData.append("ProductFat", String(fat));
    formData.append("ProductProtein", String(protein));
    formData.append("ProductCategoryName", product['productCategoryName']);
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    const addProductResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/create`,
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
    if((await addProductResponse).ok)
    {
        checker = 2;
    }
}
</script>

<main>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct'>BACK</a>
    </div>
    {#if (checker==1)}
    <div class='addingExercise'>
        <div class='exerciseContainer'>
            <fieldset>
            <legend>{product['productName']}</legend>
            <p>PER 100 GRAMS</p>
            <p>KCAL: {product['productKcal']}</p>
            <p>PROTEIN: {product['productProtein']}</p>
            <p>CARBS: {product['productCarbs']}</p>
            <p>FAT: {product['productFat']}</p>
            </fieldset>
        </div>
        <div class='exerciseContainer'>
            <form on:submit|preventDefault={addProductToMeal}>
                    <input type='number' bind:value={weight} min=0 placeholder="weight [g]" required>
                    <p>TOTAL KCAL: {kcal.toFixed(2)}</p>
                    <p>TOTAL PROTEIN: {protein.toFixed(2)}</p>
                    <p>TOTAL CARBS: {carbs.toFixed(2)}</p>
                    <p>TOTAL FAT: {fat.toFixed(2)}</p>
                <button>ADD TO MEAL</button>
            </form>
        </div>
    </div>
    {:else if (checker==2)}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
         </div>
         <meta http-equiv="refresh" content="1; url='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'"/>   
    {/if}
</main>