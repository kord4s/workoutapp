<script>
    import CheckIfLogged from "../../lib/checkIfLogged.svelte";
import Input from "../../lib/input.svelte";
    import { onMount } from "svelte";
    export let params = {}
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let productID = params.productId;
let product = [];
let weight;
let checker;
let deleteChecker=0;

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

async function modifyProduct()
{
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    let data;
    const formData = new FormData();
    formData.append("ProductName", product['productName']);
    formData.append("ProductKcal", product['productKcal']);
    formData.append("ProductWeight", weight);
    formData.append("ProductCarbs", product['productCarbs']);
    formData.append("ProductFat", product['productFat']);
    formData.append("ProductProtein", product['productProtein']);
    formData.append("ProductCategoryName", product['productCategoryName']);
    data = Object.fromEntries(formData.entries());
    data = JSON.stringify(data,null,2)
    const editProductResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/${productID}/edit`,
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
    if((await editProductResponse).ok)
    {
        checker = 2;
    }
}

async function deleteProduct(){
    if(deleteChecker==0)
    {
        deleteChecker=1;
        return;
    }

    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const deleteProductResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/${productID}/delete`,
    {
        method: 'DELETE',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await deleteProductResponse).ok)
    {
        checker = 2;
    }
}
</script>

<main>
    <CheckIfLogged/>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'>BACK</a>
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
            <form on:submit|preventDefault={modifyProduct}>
                    <input type='number' bind:value={weight} min=0 placeholder="weight [g]" required>
                    <p>TOTAL KCAL: {kcal.toFixed(0)}</p>
                    <p>TOTAL PROTEIN: {protein.toFixed(2)}</p>
                    <p>TOTAL CARBS: {carbs.toFixed(2)}</p>
                    <p>TOTAL FAT: {fat.toFixed(2)}</p>
                <button>MODIFY PRODUCT</button>
            </form>
            <form on:submit|preventDefault={deleteProduct}>
                <button>DELETE PRODUCT</button>
                {#if deleteChecker==1}
                    <p>ARE YOU SURE?</p>
                {/if}
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