<script>
    import { onMount } from "svelte";
    import Modal from "../../lib/modal.svelte";
    export let params = {}
let calendarDayID = params.calendarDayId;
let calendarID = params.calendarId;
let mealID = params.mealId;
let categories = [], products = [];
let showModal = false;
let clicked = -1;
let checker = -1;
let userSelected;
let incrementProducts=0;
onMount(async function(){
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const getCategoriesResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/getCategories`,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'
    })
    if((await getCategoriesResponse).ok)
    {
        categories = await(await getCategoriesResponse).json();
    }
})

async function searchForProducts()
{
    if(userSelected == null)
        return;
    checker=-1;
    let token = 'Bearer '+sessionStorage.getItem("token");
    let userID = sessionStorage.getItem("userID");
    const productsResponse = fetch(`https://localhost:7190/api/${userID}/calendars/${calendarID}/calendardays/${calendarDayID}/meals/${mealID}/products/getAllByCategory/${userSelected}`,
    {
        method: 'GET',
        headers:{
            'Content-Type' : 'application/json',
            'Authorization' : `${token}`
        },
        credentials: 'include',
        mode: 'cors'              
    })
    if((await productsResponse).ok)
    {
        products = await(await productsResponse).json();
        checker=1;
    }
    else
    {
        products = [];
        checker=1;
    }
    window.scrollTo(0,0);
}

function showModalOnce(exerciseId)
{
    showModal=true;
    clicked = exerciseId;
}

function increment()
{
    if((incrementProducts+1)*10 < products.length)
    incrementProducts++;
}

function decrement()
{
    if(incrementProducts>0)
        incrementProducts--;
}

</script>

<main>
    <div class='navigator'>
        <a class='narrowest' href='/#/diet/decodeCalendarDay/{calendarID}/{calendarDayID}'>BACK</a>
    </div>
    <div class='addingExercise'>
        <div class='exerciseContainer'>
            <form>
                <fieldset>
                    <legend>CHOOSE PRODUCT CATEGORY</legend>
                        {#each categories as categorie, index}
                            <div class='radio'>
                                <input type='radio' value='{categorie.productCategoryId}' id='{String(index)}' bind:group={userSelected} on:change={searchForProducts}/>
                                <label for='{String(index)}'>{categorie.productCategoryName}</label>
                            </div>
                        {/each}
                </fieldset>
            </form>
        </div>
        <div class='exerciseContainer'>
            {#if checker==1}
                <div class='exerciseToAdd'>
                    <a href='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct/newProduct/{categories[userSelected].productCategoryName}'>ADD NEW PRODUCT</a>
                </div>
                {#if products.length<10} 
                    {#each products as product}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <div class='exerciseToAdd' on:click={() => (showModalOnce(product.productId))}>
                            <!-- svelte-ignore a11y-click-events-have-key-events -->
                            <p >{product.productName}<p>
                            <Modal bind:showModal bind:clicked bind:exerciseId={product.productId}>
                                <h2>{product.productName}</h2>
                                <h5 class='productModal'>kcal: {product.productKcal}</h5>
                                <h5>protein: {product.productProtein}</h5>
                                <h5>carbs: {product.productCarbs}</h5>
                                <h5>fats: {product.productFat}</h5>
                                <div class='modalNavigator'>
                                    <a class='narrowest' href='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct/{product.productId}/additionalData'>ADD TO MEAL</a>
                                </div>
                            </Modal>
                        </div>
                    {/each}
                {:else}
                    {#each products.slice(10*incrementProducts, 10*(incrementProducts+1)) as product}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <div class='exerciseToAdd' on:click={() => (showModalOnce(product.productId))}>
                            <!-- svelte-ignore a11y-click-events-have-key-events -->
                            <p >{product.productName}<p>
                            <Modal bind:showModal bind:clicked bind:exerciseId={product.productId}>
                                <h2>{product.productName}</h2>
                                <h5>kcal: {product.productKcal}</h5>
                                <h5>protein: {product.productProtein}</h5>
                                <h5>carbs: {product.productCarbs}</h5>
                                <h5>fats: {product.productFat}</h5>
                                <div class='modalNavigator'>
                                    <a class='narrowest' href='/#/diet/{calendarID}/{calendarDayID}/{mealID}/addProduct/{product.productId}/additionalData'>ADD TO MEAL</a>
                                </div>
                            </Modal>
                        </div>
                    {/each}
                    <button class='narrowButton' on:click={decrement}>PREVIOUS PAGE</button>
                    <button class='narrowButton' on:click={increment}>NEXT PAGE</button>
                {/if}
            {/if}
        </div>
    </div>

    
</main>