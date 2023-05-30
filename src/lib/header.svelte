<script>
    let userIsLogged=sessionStorage.getItem("loginStatus");
    let username = sessionStorage.getItem("username");
</script>

<main>
    <div class='header'>
        <a href='/#' >main site</a>
        <a href='/#' >plans</a>
        {#if (userIsLogged==null)}
        <a href='/#/register' >rejestracja</a>
        <a href='/#/login' >login</a>
        {:else}
        <a href='/#/profile'>{username}</a>
        <a href='/#/logout'>log out</a>
        {/if}
    </div>
</main>