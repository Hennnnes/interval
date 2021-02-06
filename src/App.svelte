<script>
	const startTimer = event => {
		const rounds = parseInt(document.querySelector('input[name="rounds"]').value, 10);
		const round_duration = parseInt(document.querySelector('input[name="round_duration"]').value, 10);
		const round_break = parseInt(document.querySelector('input[name="round_break"]').value, 10);
		const break_duration = parseInt(document.querySelector('input[name="break_duration"]').value, 10);


		var roundInterval = setInterval(myTimer, 1000);
		var setInterval = setInterval(myTimer, 1000);

		function myTimer() {
			var d = new Date();
			var t = d.toLocaleTimeString();
			document.getElementById("demo").innerHTML = t;
		}
	}
</script>

<main>
	<h1>Intervals</h1>
	<p>I choose to do <input type="number" name="rounds" value="8"> rounds of <input type="number" value="45" name="round_duration"> Seconds with <input type="number" value="15" name="round_break"> seconds break in between.</p>
	<p>Between each round I wish a break of <input type="number" value="15" name="break_duration"> seconds.</p>
	<button on:click={startTimer}>Let's go</button>
	<p id="demo"></p>
</main>

<style>
	main {
		max-width: 60ch;
		margin: 0 auto;
	}

	input[type="number"] {
		width: 7ch;
		background-color: transparent;
		background-image: none;
		border: 0;
		border-bottom: 1px solid black;
		border-radius: 0;
		-webkit-box-shadow: none;
		-moz-box-shadow: none;
		box-shadow: none;
	}
</style>