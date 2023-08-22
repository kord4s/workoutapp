<script>
    export let name, type, placeholder, style, min, max, value;
</script>

<input style={style} id={name} placeholder={placeholder} {name} {type} min={min} max={max} value={value}>
<label for={name}>{placeholder}</label>
