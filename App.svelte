
<script lang="ts">
	import SLAbar from "./lib/SLAbar.svelte";
	import ProgressBar from "./lib/ProgressBar.svelte";

	let components = []; // Array to track spawned components

  function handleClick() {
    components = [...components, { id: Date.now() }]; // Add a new component with a unique ID
  }

</script>

<div class="grid-gap">
	<button on:click={handleClick}>Spawn Component</button>
	{#each components as component}
  <SLAbar/>
{/each}

</div>
