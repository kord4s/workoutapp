<script>
    let counter, daysChecker=false, name;

    function submitDays()
    {
        if(counter>0 && counter<=7)
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
        <h1><a href='/#/plans/days'>LETS PROCEED</a></h1>
    </div>
    {/if}
    
</main>

