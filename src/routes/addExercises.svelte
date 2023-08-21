<script>
    import { onMount } from "svelte";

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
            let calendarID;
            let workoutDaysID=[];
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
                    const gettingCalendarResponse = fetch("https://localhost:7190/api/"+userID+"/calendars/getAllUsersCalendars",
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
                        calendarID = calendarCreationResult;
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
                let calendarDayID = calendarDayCreationResult;
                console.log(calendarDayID);      
            }

            //creating base workout days

            for(let i=0; i<Number(daysCount); i++)
            {
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
    })
</script>

<main>
</main>