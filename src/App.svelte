<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300&display=swap" rel="stylesheet">
</svelte:head>

<script>
	const verbs = [{
		name: "Poner",
		indicPresent: ['pongo', 'pones', 'pone', 'ponemos', 'ponéis', 'ponen']
	}]
	let conjugations = ['', '', '', '', '', ''];
	const forms = ['Yo', 'Tu', 'El/Ella/Usted', 'Nosotros', 'Vosotros', 'Ellos/Ellas/Ustedes'];
	let responses = [];
	let correct = true;
	
	function handleSubmit() {
		// dont update variables until done as to not trigger update
		let l_correct = true;
		let l_responses = [];
		
		conjugations.forEach((c, i) => {
			c = c.replaceAll("^a", "á");
			c = c.replaceAll("^e", "é");
			c = c.replaceAll("^i", "í");
			c = c.replaceAll("^o", "ó");
			c = c.replaceAll("^u", "ú");
			c = c.replaceAll("^^u", "ü");
			c = c.replaceAll("^n", "ñ");

			if(c !== verbs[0].indicPresent[i]) {
				l_correct = false;
				l_responses = l_responses.concat([`${c === "" ? "blank" : c} should be ${verbs[0].indicPresent[i]}`]);
			}
		});

		correct = l_correct;
		responses = l_responses;
	}
</script>

<main>
	<h1>Verb: {verbs[0].name}</h1>
	<p>Your last answer was {correct ? "correct" : "incorrect"}</p>
	{#if !correct}
		{#each responses as r}
			<p>{r}</p>
		{/each}
	{/if}

	{#each [0, 1, 2, 3, 4, 5] as c}
		<input bind:value={conjugations[c]} placeholder={forms[c]}>
		<br>
	{/each}
	<button on:click={handleSubmit}>
		Check
	</button>

	<p>Pro Tip: place ^ infront of accented characters ( ^n = ñ ) and ^^u for ü</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		font-family: 'Raleway', sans-serif;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>