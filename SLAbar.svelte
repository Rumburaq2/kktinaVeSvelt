
<script lang="ts">

    import ProgressBar from "./ProgressBar.svelte";

	let startDateTime = "01/02/2025 14:30";
    let endDateTime = "01/02/2025 14:31";

</script>

<div class="grid-gap">
	<ProgressBar {startDateTime} {endDateTime}/>
</div>
