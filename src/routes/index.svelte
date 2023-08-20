<script>
    let loginStatus = sessionStorage.getItem("loginStatus");
    let username = sessionStorage.getItem("username");
</script>
    

<main>
    <div class="container">
    {#if loginStatus != null}
        <div class="welcome_logged"></div>
        <div class="welcome_logged"></div>

    {:else}
        <div class="welcome_notlogged">
            <h1>MAKE YOUR LIFE EASIER WITH HEALTHY LIFESTYLE</h1>
            <h1><a href='/#/register'>REGISTER</a> OR <a href='/#/login'>LOG IN</a></h1>
        </div>

    {/if}
    </div>

</main>