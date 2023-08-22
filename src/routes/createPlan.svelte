<script>
    import { is_empty } from "svelte/internal";

    let counter, daysChecker=false, name;

    function submitDays()
    {
        if(counter>0 && counter<=7 && name != null )
        daysChecker=true;
        sessionStorage.setItem("daysCount", counter);
        sessionStorage.setItem("Name", name);
    }
</script>

<main>
    {#if (!daysChecker)}
    <div>
    <input placeholder='1-7' type='number' min='1' max='7' bind:value={counter}>
    <input placeholder="name of workout plan" type='text' max='30' bind:value={name}>
    <button on:click={submitDays}></button>
    </div>
    {:else if (daysChecker)}
    <div>
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
         </div>
        <meta http-equiv="refresh" content="2; url='/#/plans/create/days'"/>
    </div>
    {/if}
    
</main>

