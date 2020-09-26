<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300&display=swap" rel="stylesheet">
</svelte:head>

<script>
	import * as firebase from "firebase/app";

	import 'firebase/firestore';
	
	var firebaseConfig = {
		apiKey: "AIzaSyAyvaVVZs8goGeUch0lvhQhlo4Ika0WyGY",
		authDomain: "conjugations-8df51.firebaseapp.com",
		databaseURL: "https://conjugations-8df51.firebaseio.com",
		projectId: "conjugations-8df51",
		storageBucket: "conjugations-8df51.appspot.com",
		messagingSenderId: "792949381129",
		appId: "1:792949381129:web:ae7f5e00984e5fdae380d8",
		measurementId: "G-71MBDCTS7P"
	};
	// Initialize Firebase
	firebase.initializeApp(firebaseConfig);
	const db = firebase.firestore();
	let verbs = getVerbs();

	let conjugations = ['', '', '', '', '', ''];
	const forms = ['Yo', 'Tu', 'El/Ella/Usted', 'Nosotros', 'Vosotros', 'Ellos/Ellas/Ustedes'];
	let responses = [];
	let correct = true;

	async function getVerbs() {
		/* move this into a cloud function at some point */
		const verbsRef = db.collection('verbs').doc('poner');
		const verb = await verbsRef.get();
		return verb;
	}
	
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
	{#await verbs}
		<h1>Verb: Loading...</h1>
	{:then verbs} 
		<h1>Verb: {verbs.data().name}</h1>
	{/await}
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