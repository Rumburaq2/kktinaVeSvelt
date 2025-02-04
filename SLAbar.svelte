<script lang="ts">
    import ProgressBar from "./ProgressBar.svelte";

	let startDateTime = "01/02/2025 14:30";//defalut value
    //calculate timestamp DD/MM/YYYY HH:MM of current time
    const now = new Date();
    const day = String(now.getDate()).padStart(2, '0');
    const month = String(now.getMonth() + 1).padStart(2, '0'); // Months are zero-based
    const year = now.getFullYear();
    const hours = String(now.getHours()).padStart(2, '0');
    const minutes = String(now.getMinutes()).padStart(2, '0');
	startDateTime = `${day}/${month}/${year} ${hours}:${minutes}`;
	startDateTime = "05/02/2025 06:31";

	let endDateTime = "01/02/2025 22:31"; //default value
    let secondsEpoch = getSecondsSinceEpoch(startDateTime);

	const SEVERITY ={
		LOW: 12,
	  	INFORMATIONAL: 24,
		MEDIUM: 6,
		HIGH: 1
  	};
	let severity = SEVERITY.HIGH;


	if(severity === SEVERITY.LOW){
        //end time is startTime + 12h
        secondsEpoch = secondsEpoch+12*3600;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }
    else if(severity === SEVERITY.MEDIUM){
        //end time is startTime + 6h
        secondsEpoch = secondsEpoch+6*3600;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }
    else if(severity === SEVERITY.HIGH){
        //end time is startTime + 1h
        secondsEpoch = secondsEpoch+1200;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }

	console.log("endTime: "+endDateTime);

	const workingHours = {
    	start: 8, // 8:00 AM
    	end: 20 // 8:00 PM
  	};

	const workingDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'];

	let result;
	result = getClosestWorkingTime(startDateTime);
	console.log("startDateTime: "+startDateTime);
	result = formatDateTime(result);
	console.log("result: "+result);

    function getSecondsSinceEpoch(dateString) {
        // Split the input string into date and time parts
        const [datePart, timePart] = dateString.split(' ');

        // Split the date part into day, month, and year
        const [day, month, year] = datePart.split('/');

        // Split the time part into hours and minutes
        const [hours, minutes] = timePart.split(':');

        // Create a Date object (note: months are 0-based in JavaScript)
        const date = new Date(year, month - 1, day, hours, minutes);

        // Get the number of seconds since the Unix epoch
        const secondsSinceEpoch = Math.floor(date.getTime() / 1000);

        return secondsSinceEpoch;
    }

    function getFormattedDateFromTimestamp(timestamp) {
    // Create a Date object from the timestamp (multiply by 1000 to convert seconds to milliseconds)
    const date = new Date(timestamp * 1000);

    // Extract day, month, year, hours, and minutes
    const day = String(date.getDate()).padStart(2, '0'); // Ensure 2 digits
    const month = String(date.getMonth() + 1).padStart(2, '0'); // Months are 0-based
    const year = date.getFullYear();
    const hours = String(date.getHours()).padStart(2, '0'); // Ensure 2 digits
    const minutes = String(date.getMinutes()).padStart(2, '0'); // Ensure 2 digits

    // Format the date and time as DD/MM/YYYY HH:MM
    const formattedDate = `${day}/${month}/${year} ${hours}:${minutes}`;

    return formattedDate;
}

	function parseDateTime(dateTime) {
    	const [date, time] = dateTime.split(' ');
    	const [day, month, year] = date.split('/').map(Number);
    	const [hours, minutes] = time.split(':').map(Number);
   	 return new Date(year, month - 1, day, hours, minutes);
  }

    function formatDateTime(date) {
		if (!(date instanceof Date)) {
      		return true;
    	}
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



</script>

<div class="grid-gap">
	<main>
  <p>Input Date and Time: {startDateTime}</p>
  {#if result === true}
    <p>The provided time is within working hours.</p>
	  <ProgressBar startDateTime={startDateTime} {endDateTime}/>
  {:else}
    <p>The closest working time is: {result}</p>
	  <ProgressBar startDateTime={result} endDateTime={endDateTime}/>
  {/if}
</main>


</div>
