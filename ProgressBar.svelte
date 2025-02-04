
<script lang="ts">

    let { startDateTime = "01/02/2025 14:30", endDateTime = "01/02/2025 14:31" } = $props();
    let timecomputed = calculateDuration();
	let elapsed = $state(0);
	let duration = $state(timecomputed);
	let interval: number
    //let severity;

	function start() {
		interval = setInterval(() => {
            let now = new Date();
            let day = String(now.getDate()).padStart(2, '0');
            let month = String(now.getMonth() + 1).padStart(2, '0'); // Months are zero-based
            let year = now.getFullYear();
            let hours = String(now.getHours()).padStart(2, '0');
            let minutes = String(now.getMinutes()).padStart(2, '0');
            let seconds = String(now.getSeconds()).padStart(2, '0');

            let currDateTime = `${day}/${month}/${year} ${hours}:${minutes}:${seconds}`;

            let secondsEpoch = getSecondsSinceEpoch(currDateTime);
			let secondsEpoch2 = getSecondsSinceEpoch(startDateTime);

            // console.log(startDateTime)
           // console.log(endDateTime)
           // console.log(secondsEpoch2)
           // console.log()
            console.log(secondsEpoch)
           // console.log(elapsed)
			elapsed = secondsEpoch - secondsEpoch2;
			console.log("elapsed: "+elapsed);
			if (elapsed > duration) {
				elapsed = duration
				clearInterval(interval)
			}
		}, 1000)
	}

     function getSecondsSinceEpoch(dateString) {
        // Split the input string into date and time parts
        const [datePart, timePart] = dateString.split(' ');

        // Split the date part into day, month, and year
        const [day, month, year] = datePart.split('/');

        // Split the time part into hours and minutes
        const [hours, minutes, seconds] = timePart.split(':');

        // Create a Date object (note: months are 0-based in JavaScript)
        const date = new Date(year, month - 1, day, hours, minutes, seconds);

        // Get the number of seconds since the Unix epoch
        const secondsSinceEpoch = Math.floor(date.getTime() / 1000);

        return secondsSinceEpoch;
    }


	function reset() {
		elapsed = 0
		clearInterval(interval)
		start()
	}

	$effect(() => {
		if (!duration) return
		start()
		return () => clearInterval(interval)
	})

    function calculateDuration() {
    try {
        // Extract date and time components from startDateTime
        let [date1, time1] = startDateTime.split(" ");
        let [d1, m1, y1] = date1.split("/").map(Number);
        let [h1, min1, s1] = time1.split(":").map(Number);

        // Extract date and time components from endDateTime
        let [date2, time2] = endDateTime.split(" ");
        let [d2, m2, y2] = date2.split("/").map(Number);
        let [h2, min2, s2] = time2.split(":").map(Number);

        // Convert to JS Date objects (JS months are 0-based)
        let start = new Date(y1, m1 - 1, d1, h1, min1, s1);
        let end = new Date(y2, m2 - 1, d2, h2, min2, s2);

        // Calculate the difference in milliseconds
        let diffMs = end - start;

        // If the end date is before the start date, return 0
        if (diffMs < 0) {
            return 0;
        }

        // Convert milliseconds to seconds
        let totalSeconds = Math.floor(diffMs / 1000);
        return totalSeconds;
    } catch (error) {
        console.error("Invalid date format:", error);
        return null; // Return null in case of an error
    }
}

    console.log(calculateDuration());
</script>

<div class="grid-gap">
	<div>
      <p>{startDateTime} - {endDateTime}</p>
		<label>
			<span>Elapsed time:</span>
			<progress max={duration} value={elapsed}></progress>
		</label>

		<div>{elapsed.toFixed(1)}s</div>
	</div>

    {#if duration === elapsed}
        <p>SLA breached!</p>
        {/if}
	<!-- <button onclick={reset}>Reset</button> -->
</div>
