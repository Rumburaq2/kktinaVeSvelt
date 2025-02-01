
<script lang="ts">

    let { startDateTime = "01/02/2025 14:30", endDateTime = "01/02/2025 14:31" } = $props();
    let timecomputed = calculateDuration();
	let elapsed = $state(0);
	let duration = $state(timecomputed);
	let interval: number
    //let severity;

	function start() {
		interval = setInterval(() => {
			elapsed += 1
			if (elapsed > duration) {
				elapsed = duration
				clearInterval(interval)
			}
		}, 1000)
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
            // Extract date and time components
            let [date1, time1] = startDateTime.split(" ");
            let [date2, time2] = endDateTime.split(" ");

            let [d1, m1, y1] = date1.split("/").map(Number);
            let [d2, m2, y2] = date2.split("/").map(Number);

            let [h1, min1] = time1 ? time1.split(":").map(Number) : [0, 0];
            let [h2, min2] = time2 ? time2.split(":").map(Number) : [0, 0];

            // Convert to JS Date objects (JS months are 0-based)
            let start = new Date(y1, m1 - 1, d1, h1, min1);
            let end = new Date(y2, m2 - 1, d2, h2, min2);
            let totalTime = 0;

            // Calculate the difference in milliseconds
            let diffMs = end - start;

            if (diffMs < 0) {
                totalTime = 0;
                return;
            }

            // Convert milliseconds to days, hours, minutes, and seconds
            let totalSeconds = Math.floor(diffMs / 1000);
            //let days = Math.floor(totalSeconds / 86400);
            //let hours = Math.floor((totalSeconds % 86400) / 3600);
            //let minutes = Math.floor((totalSeconds % 3600) / 60);
            //let seconds = totalSeconds % 60;
            totalTime = totalSeconds;
            return totalTime;
        } catch (error) {
            console.error("Invalid date format:", error);
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

	<!-- <button onclick={reset}>Reset</button> -->
</div>
