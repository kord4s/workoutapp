<script>
	export let showModal; // boolean
	export let clicked;
	export let exerciseId
	let dialog; // HTMLDialogElement
	$: if (dialog && showModal && clicked === exerciseId) dialog.showModal();
</script>

<!-- svelte-ignore a11y-click-events-have-key-events a11y-no-noninteractive-element-interactions -->
<dialog
	bind:this={dialog}
	on:close={() => (showModal = false)}
	on:click|self={() => dialog.close()}
>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<div on:click|stopPropagation>
		<slot name="header" />
		<slot />
		<!-- svelte-ignore a11y-autofocus -->
	</div>
</dialog>

<style>
	dialog {
		background-color: rgb(92, 90, 90);
		min-width:35%;
		max-width: 45%;
		border-radius: 0.2em;
		border: none;
		padding:0;
		color:black;
		border-radius: 40px;
	}

	dialog > div > h5
	{
	text-align: center !important;
	}


	dialog::backdrop {
		background: rgba(0, 0, 0, 0.6);
		transition: 0.5;
	}	
	dialog[open] {
		animation: zoom 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
	}
	@keyframes zoom {
		from {
			transform: scale(0.95);
		}
		to {
			transform: scale(1);
		}
	}
	dialog[open]::backdrop {
		animation: fade 0.2s ease-out;
	}
	@keyframes fade {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}

</style>
