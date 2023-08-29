<script>
    import { onMount } from "svelte";
    export let params = {}
    let calendarDayID = params.calendarDayId;
    let calendarID = params.calendarId;
    let stringDate;
    let decodedDay="", decodedMonth="", decodedYear=""; 
    onMount(async function(){
        let token = 'Bearer '+sessionStorage.getItem("token");
        let userID = sessionStorage.getItem("userID");
        const gettingCalendarDaysResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/"+calendarDayID,
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'                    
            })
            if((await gettingCalendarDaysResponse).status)
            {
                calendarDayID = await(await gettingCalendarDaysResponse).json();
                stringDate = calendarDayID['calendarDate'];
                let counter=0;
                for(let i=0; i<stringDate.length; i++)
                {
                    if(stringDate[i]!='-')
                    {
                        switch(counter)
                        {
                            case 0:
                                decodedDay+=stringDate[i];
                                break;
                            case 1:
                                decodedMonth+=stringDate[i];
                                break;
                            case 2:
                                decodedYear+=stringDate[i];
                                break;
                            default:
                                break;
                        }
                    }
                    else
                    {
                        counter++;
                    }
                }
                location.replace(`/#/diet/overview/${decodedDay}/${decodedMonth}/${decodedYear}`);
            }
    })
</script>

<main>
    
</main>