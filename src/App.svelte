<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300&display=swap" rel="stylesheet">
</svelte:head>

<script>
	/* Imports */
	import Quiz from './Quiz.svelte';
	import Create from './Create.svelte';
	import * as firebase from 'firebase/app';
	import 'firebase/firestore';

	/* Global Vars */
	let showQuiz = true;

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

	firebase.initializeApp(firebaseConfig);
	let db = firebase.firestore();
</script>

<main>
	<button on:click={() => showQuiz = !showQuiz}>{showQuiz ? "+ Word" : "Quiz"}</button>

	{#if showQuiz}
		<Quiz db={db}/>
	{:else}
		<Create db={db}/>
	{/if}

</main>