<script>
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
    <div class='navigator'>
        <a class='narrowest' href='/#/plans/'>BACK</a>
    </div>
    {#if (!daysChecker)}
    <div class='plans'>
        <form on:submit|preventDefault={submitDays}>
            <input placeholder='how many base days?' type='number' min='1' max='7' bind:value={counter} required>
            <input placeholder="name of your workout plan" type='text' max='30' bind:value={name} required>
            <button>CREATE</button>
        </form>
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

