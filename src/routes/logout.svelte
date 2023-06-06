<script>
    import { onMount } from "svelte";
    let checker=false;
    
    onMount(function(){
        sessionStorage.removeItem("username");
        sessionStorage.removeItem("loginStatus");
        sessionStorage.removeItem("token");
        checker=true;
    })

    
</script>


<h1>logging out</h1>
<div class='container'>
    <svg class="spinner" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
    <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
    </svg>
 </div>

 {#if checker==true}
    <meta http-equiv="refresh" content="2; url='/#/'"/>
 {/if}
