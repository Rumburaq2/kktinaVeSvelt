
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

    const full = `${day}/${month}/${year} ${hours}:${minutes}`;
    startDateTime = full;

    //calculate endtime based based on severity
    let endDateTime = "01/02/2025 14:31"; //default value
    let secondsEpoch = getSecondsSinceEpoch(startDateTime);

    let severity = "High";
    if(severity === "Low"){
        //end time is startTime + 12h
        secondsEpoch = secondsEpoch+12*3600;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }
    else if(severity === "Medium"){
        //end time is startTime + 6h
        secondsEpoch = secondsEpoch+6*3600;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }
    else if(severity === "High"){
        //end time is startTime + 1h
        secondsEpoch = secondsEpoch+120;
        endDateTime = getFormattedDateFromTimestamp(secondsEpoch);
    }

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

</script>

<div class="grid-gap">
	<ProgressBar {startDateTime} {endDateTime}/>
</div>
