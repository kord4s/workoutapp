<script>
    import { onMount } from "svelte";

    let userIsLogged=sessionStorage.getItem("loginStatus");
    let username = sessionStorage.getItem("username");
    let check=false;

    function removeSessionItems()
    {
        sessionStorage.removeItem("loginStatus");
        sessionStorage.removeItem("username");
        sessionStorage.removeItem("token");
        sessionStorage.removeItem("userID");
    }

    
    onMount(
    async function checkLoginStatus()
    {
        if(sessionStorage.getItem("token") === null || sessionStorage.getItem("loginStatus") === null)
        {
            sessionStorage.clear();
            return;
        }
        let token = "Bearer "+sessionStorage.getItem("token");
        if(userIsLogged = "true")
        {
            const response = fetch('https://localhost:7190/api/user/logged',
            {
                method: 'GET',
                credentials: 'include',
                mode: 'cors',
                headers: {
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                }
            }
            )
            if((await response).ok)
            {
                const data = await(await response).json();
                if(data['userId'] != null)
                {
                    sessionStorage.setItem("userID", data['userId']);
                    check=true;
                }
                else
                {
                    check=false;
                    removeSessionItems();
                }
                    
            }
            else
            {
                check=false;
                removeSessionItems();
            }
        }
    })
   

</script>

<main>
    <div class='header'>
        <a href='/#' >main site</a>
        <a href='/#/plans' >plans</a>
        <a href='/#/diet/overview'>diet</a>
        {#if (!check)}
        <a href='/#/register' >rejestracja</a>
        <a href='/#/login' >login</a>
        {:else}
        <a href='/#/profile'>{username}</a>
        <a href='/#/logout'>log out</a>
        {/if}
    </div>
</main>