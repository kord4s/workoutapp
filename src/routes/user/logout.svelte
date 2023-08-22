<script>
    import { onMount } from "svelte";
    let checker=false;
    
    onMount(function(){
        sessionStorage.clear();
        checker=true;
    })

    
</script>

<div class='container'>
    <svg class="spinner" style="margin-top:10vw" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg">
    <circle name="logout" class="path" fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30"></circle>
    </svg>
 </div>

 {#if checker==true}
    <meta http-equiv="refresh" content="2; url='/#/'"/>
 {/if}
