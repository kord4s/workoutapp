<script>
    import Input from "../lib/input.svelte";
    let loginCheck=0, data={}, result;
    function tryToLogin(e)
    {
        e.preventDefault()
        const formData = new FormData(e.target);
        data = Object.fromEntries(formData.entries());
        data = JSON.stringify(data,null,2)
        login(data);
    }

    async function login(data){
        const response = fetch('https://localhost:7190/api/users/login',
        {
            method: 'POST',
            body: data,
            headers:{'Content-Type' : 'application/json'},
            credentials: 'include',
            mode: 'cors'
        });
        if((await response).ok)
        {
            data = JSON.parse(data);
            result = await(await response).json()
            sessionStorage.setItem("username", data['username']);
            sessionStorage.setItem("token", result['token']);
            sessionStorage.setItem("loginStatus", 'true');
            loginCheck=1;
        }
        else
            loginCheck=-1;
    }
        //
</script>


<main>
    <form class='account' on:submit={tryToLogin}>
        <Input name="username" type="text" placeholder='LOGIN' style=''/>
        <Input name="password" type="password" placeholder='PASSWORD' style=''/>
        <button>LOG IN</button>
    </form>
</main>


{#if (loginCheck==1)}
<meta http-equiv="refresh" content="2; url='/#/'"/>
<p style="text-align: center;">sukces, poczekaj na przekierowanie</p>
{:else if (loginCheck==-1)}
<p style="text-align: center;">spróbuj ponownie</p>
{/if}