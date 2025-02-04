
<script lang="ts">
	import SLAbar from "./lib/SLAbar.svelte";
	import ProgressBar from "./lib/ProgressBar.svelte";

	let components = []; // Array to track spawned components

  function handleClick() {
    components = [...components, { id: Date.now() }]; // Add a new component with a unique ID
  }


  import { onMount } from 'svelte';

  const workingHours = {
    start: 8, // 8:00 AM
    end: 20 // 8:00 PM
  };

  const workingDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'];

  let startDateTime = "01/02/2025 14:30"; // default value

  // Calculate timestamp DD/MM/YYYY HH:MM of current time
  const now = new Date();
  const day = String(now.getDate()).padStart(2, '0');
  const month = String(now.getMonth() + 1).padStart(2, '0'); // Months are zero-based
  const year = now.getFullYear();
  const hours = String(now.getHours()).padStart(2, '0');
  const minutes = String(now.getMinutes()).padStart(2, '0');

  startDateTime = `${day}/${month}/${year} ${hours}:${minutes}`;
  startDateTime = "31/03/2025 02:22";

  function parseDateTime(dateTime) {
    const [date, time] = dateTime.split(' ');
    const [day, month, year] = date.split('/').map(Number);
    const [hours, minutes] = time.split(':').map(Number);
    return new Date(year, month - 1, day, hours, minutes);
  }

   function formatDateTime(date) {
    const day = String(date.getDate()).padStart(2, '0');
    const month = String(date.getMonth() + 1).padStart(2, '0'); // Months are zero-based
    const year = date.getFullYear();
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    return `${day}/${month}/${year} ${hours}:${minutes}`;
  }

  function getClosestWorkingTime(inputDateTime) {
    const inputDate = parseDateTime(inputDateTime);
    const currentDayIndex = inputDate.getDay(); // 0 for Sunday, 1 for Monday, etc.
    const currentHour = inputDate.getHours();

    // Convert day index to a string name
    const dayName = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];

    // Check if it's a working day and within working hours
    if (
      workingDays.includes(dayName[currentDayIndex]) &&
      currentHour >= workingHours.start &&
      currentHour < workingHours.end
    ) {
      return true;
    }

    // Calculate the closest working time
    const closestTime = new Date(inputDate);

    // If it's outside working hours but on a working day
    if (workingDays.includes(dayName[currentDayIndex])) {
      if (currentHour < workingHours.start) {
        closestTime.setHours(workingHours.start, 0, 0, 0);
        return closestTime;
      }

      if (currentHour >= workingHours.end) {
        let daysToAdd = 1;
        while (!workingDays.includes(dayName[(currentDayIndex + daysToAdd) % 7])) {
          daysToAdd++;
        }
        closestTime.setDate(closestTime.getDate() + daysToAdd);
      }
    } else {
      // If it's not a working day, find the next working day
      let daysToAdd = 1;
      while (!workingDays.includes(dayName[(currentDayIndex + daysToAdd) % 7])) {
        daysToAdd++;
      }
      closestTime.setDate(closestTime.getDate() + daysToAdd);
    }

    // Set the time to the start of working hours
    closestTime.setHours(workingHours.start, 0, 0, 0);
    return closestTime;
  }

  let result;

  onMount(() => {
    result = getClosestWorkingTime(startDateTime);
	result = formatDateTime(result);
  });
</script>

<div class="grid-gap">
	<button on:click={handleClick}>Spawn Component</button>
	{#each components as component}
  <SLAbar/>
{/each}


<main>
  <p>Input Date and Time: {startDateTime}</p>
  {#if result === true}
    <p>The provided time is within working hours.</p>
  {:else}
    <p>The closest working time is: {result}</p>
  {/if}
</main>



</div>
