<script>
    let loginStatus = sessionStorage.getItem("loginStatus");
    let username = sessionStorage.getItem("username");
</script>
    

<main>
    {#if loginStatus != null}

    zalogowany

    {:else}

    niezalogowany

    {/if}


</main>