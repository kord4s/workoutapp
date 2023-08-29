<script>
    import { onMount } from "svelte";

onMount(function(){
    if(!sessionStorage.getItem("loginStatus"))
    {
        location.replace("/#/");
    }
})
</script>