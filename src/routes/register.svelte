<script>
    import Input from "../lib/input.svelte";
    let registerCheck=0, data = {}, result;
    function tryToRegister(e)
    {
        e.preventDefault()
        const formData = new FormData(e.target);
        data = Object.fromEntries(formData.entries());
        data = JSON.stringify(data,null,2)
        register(data);
    }

    async function register(data){
        try
        {
            const response = fetch('https://localhost:7190/api/users/register',
            {
                method: 'POST',
                body: data,
                credentials: 'include',
                mode: 'cors',
                headers:{'Content-Type' : 'application/json'},
            });
            
            if((await response).ok)
                registerCheck=1;
            else
                registerCheck=-1;
        } catch (a) {}
    };    


</script>


<main>
    <form class='account' on:submit={tryToRegister}>
        <Input name="username" type="text" placeholder='LOGIN' style=''/>
        <Input name="email" type="email" placeholder='EMAIL' style=''/>
        <Input name="password" type="password" placeholder='PASSWORD' style=''/>
        <button>REGISTER</button>
    </form>
</main>

{#if (registerCheck==1)}
<meta http-equiv="refresh" content="2; url='/#/login'"/>
<p style="text-align: center;">sukces, poczekaj na przekierowanie</p>
{:else if (registerCheck==-1)}
<p style="text-align: center;">spróbuj ponownie</p>
{/if}