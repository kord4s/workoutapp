<script>
    import { onMount } from "svelte";
    let checker = -1;
    onMount(async function(){

        let daysCount = sessionStorage.getItem("daysCount");
        let name = sessionStorage.getItem("Name");
        let token = 'Bearer '+sessionStorage.getItem("token");
        let userID = sessionStorage.getItem("userID");
        let formData = new FormData(); 
        formData.append("daysCount", daysCount);
        formData.append("name", name);
        formData.append("userid", userID);
        let data = Object.fromEntries(formData.entries());
        let dataJSON = JSON.stringify(data,null,2);

        const response = fetch("https://localhost:7190/api/"+userID+"/workoutplans/create",
        {
            method: 'POST',
            body: dataJSON,
            headers:{
                'Content-Type' : 'application/json',
                'Authorization' : `${token}`
            },
            credentials: 'include',
            mode: 'cors'              
        })

        if((await response).ok)
        {
            let result = await(await response).json()
            let workoutPlanID = result;
            sessionStorage.setItem("workoutPlanId", workoutPlanID);
            let calendarID=[];
            let workoutDaysID=[];
            let calendarDayID=[];
            console.log(workoutPlanID);
            //checking if calendar is already existing
            const calendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/count",
            {
                method: 'GET',
                headers:{
                    'Content-Type' : 'application/json',
                    'Authorization' : `${token}`
                },
                credentials: 'include',
                mode: 'cors'
            })
            if((await calendarResponse).ok)
            {
                let calendarResult = await(await calendarResponse).json();
                if(calendarResult!=0)
                {
                    const gettingCalendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/getAllUserCalendars",
                    {
                    method: 'GET',
                    headers:{
                        'Content-Type' : 'application/json',
                        'Authorization' : `${token}`
                    },
                    credentials: 'include',
                    mode: 'cors'                    
                    })
                    if((await gettingCalendarResponse).ok)
                    {
                        let calendarCreationResult = await(await gettingCalendarResponse).json()
                        calendarID = calendarCreationResult[0]['calendarId'];
                        console.log(calendarID);   
                    }
                }
                else
                {
                    const calendarCreationResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/create",
                    {
                    body: '{}',
                    method: 'POST',
                    headers:{
                        'Content-Type' : 'application/json',
                        'Authorization' : `${token}`
                    },
                    credentials: 'include',
                    mode: 'cors'                    
                    })
                    if((await calendarCreationResponse).ok)
                    {
                        let calendarCreationResult = await(await calendarCreationResponse).json()
                        calendarID = calendarCreationResult;
                        console.log(calendarID);   
                    }
                }
            }

            //getting calendar day for creation of workout plan days

            let formCalendarData = new FormData(); 
            formCalendarData.append("CalendarDate", "base");
            let calendarData = Object.fromEntries(formCalendarData.entries());
            let calendarDataJSON = JSON.stringify(calendarData,null,2);
            for(let i=0; i<(parseInt(daysCount)); i++)
            {
                const creatingCalendarDaysResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/"+calendarID+"/calendardays/create",
                        {
                        body: calendarDataJSON,
                        method: 'POST',
                        headers:{
                            'Content-Type' : 'application/json',
                            'Authorization' : `${token}`
                        },
                        credentials: 'include',
                        mode: 'cors'                    
                        })
                if((await creatingCalendarDaysResponse).ok)
                {
                    let calendarDayCreationResult = await(await creatingCalendarDaysResponse).json()
                    calendarDayID[i] = calendarDayCreationResult;
                    console.log(calendarDayID[i]);      
                }
            }

            //creating base workout days

            for(let i=0; i<(parseInt(daysCount)); i++)
            {
                let formCalendarData = new FormData();
                formCalendarData.append("CalendarDate", "base");
                formCalendarData.append("CalendarDayId", calendarDayID[i]);
                let calendarData = Object.fromEntries(formCalendarData.entries());
                let calendarDataJSON = JSON.stringify(calendarData,null,2);
                console.log(calendarDataJSON);
                const creatingWorkoutDaysResponse = fetch("https://localhost:7190/api/"+userID+"/workoutplans/"+workoutPlanID+"/workoutdays/create",
                {
                    method: 'POST',
                    body: calendarDataJSON,
                    headers:{
                        'Content-Type' : 'application/json',
                        'Authorization' : `${token}`
                    },
                    credentials: 'include',
                    mode: 'cors'              
                })
                if((await creatingWorkoutDaysResponse).ok)
                {
                    let creatingWorkoutDaysResult = await(await creatingWorkoutDaysResponse).json()
                    let WorkoutDayID = creatingWorkoutDaysResult;
                    workoutDaysID[i]=WorkoutDayID
                    console.log(WorkoutDayID);   
                }
            }            
        }
        sessionStorage.removeItem("daysCount");
        checker = 1;
    })
</script>

<main>
    {#if (checker == 1)}
        <h1><a href="/#/plans/overview">LETS PROCEED</a></h1>
    {:else}
        <div class='container'>
            <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
            <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
            </svg>
        </div>
    {/if}
</main>