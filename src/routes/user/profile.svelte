<script>
    import Input from "../../lib/input.svelte";
    let data = {}, emailChecker=0, passwordChecker=0, userID = sessionStorage.getItem("userID"), whichToChange;


    function changeUserData(e)
    {
        e.preventDefault()
        const formData = new FormData(e.target);
        data = Object.fromEntries(formData.entries());
        if(formData.has("password"))
        {
            whichToChange="password";
            if(data["password"] === data["password_repeat"])
            {
                formData.delete("password_repeat");
                data = Object.fromEntries(formData.entries());
                passwordChecker=1;
            }
            else
            {
                passwordChecker=-1;
                return;
            }
        }
        else
        {
            whichToChange="email";
            if(data["email"] === data["email_repeat"])
            {
                formData.delete("email_repeat");
                data = Object.fromEntries(formData.entries());
                emailChecker=1;
            }
            else
            {
                emailChecker=-1;
                return;
            }
        }
        data = JSON.stringify(data,null,2)
        tryToChangeData(data);
    }

    async function tryToChangeData(data)
    {
        let token = "Bearer "+sessionStorage.getItem("token");
        let controller = 'https://localhost:7190/api/user/'+userID+'/change/'+whichToChange;
        const response = fetch(controller,
            {
                method: 'PUT',
                body: data,
                headers: {
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'
                
            })
        if((await response).ok)
        {
            switch(whichToChange)
            {
                case "password":
                    passwordChecker=1;
                    break;
                case "email":
                    emailChecker=1;
                    break;
                default:
                    break;
            }
        }
        else
        {
            switch(whichToChange)
            { 
                case "password":
                    passwordChecker=-1;
                    break;
                case "email":
                    emailChecker=-1;
                    break;
                default:
                    break;
            }            
        }
        
    }
</script>

<main>
    <form class='account' on:submit={changeUserData}>
        <h1>CHANGE EMAIL</h1>
        <Input name="email" type="email" placeholder='NEW EMAIL' style=''/>
        <Input name="email_repeat" type="email" placeholder='REPEAT EMAIL' style=''/>
        <button>CHANGE EMAIL</button>
    </form>

    {#if (emailChecker==1)}
    <meta http-equiv="refresh" content="2; url='/#/'"/>
    <p style="text-align: center;">sukces, poczekaj na przekierowanie</p>
    {:else if (emailChecker==-1)}
    <p style="text-align: center;">spróbuj ponownie</p>
    {/if}


    
    <form class='account' on:submit={changeUserData}>
        <h1>CHANGE PASSWORD</h1>
        <Input name="password" type="password" placeholder='NEW PASSWORD' style=''/>
        <Input name="password_repeat" type="password" placeholder='REPEAT PASSWORD' style=''/>
        <button>CHANGE PASSWORD</button>
    </form>

    {#if (passwordChecker==1)}
    <p style="text-align: center;">sukces, poczekaj na przekierowanie</p>
    <meta http-equiv="refresh" content="2; url='/#/'"/>
    {:else if (passwordChecker==-1)}
    <p style="text-align: center;">spróbuj ponownie</p>
    {/if}

</main>