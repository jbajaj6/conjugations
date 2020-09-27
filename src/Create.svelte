<svelte:head>
	<link href="https://fonts.googleapis.com/css2?family=Raleway:wght@300&display=swap" rel="stylesheet">
</svelte:head>

<script>
    export let db;

    const forms = ['Yo', 'Tu', 'El/Ella/Usted', 'Nosotros', 'Vosotros', 'Ellos/Ellas/Ustedes'];
    let conjugations = ['', '', '', '', '', '']
    let name = "";
    let savedName = "";
    let saved = false;

    let verbRef = createRef();

    async function createRef() {
        let l_ref = await db.collection('verbs');
        return l_ref;
    }

    async function createDoc() {
        const data = {
            name: name.toLowerCase(),
            indicPresent: conjugations,
            verified: false
        };

        const res = await db.collection('verbs').doc(`${name}`).set(data);
    }

    async function createVerb() {
        conjugations.forEach((c, i) => {
            c = c.toLowerCase();
            c = c.replaceAll("^a", "á");
            c = c.replaceAll("^e", "é");
            c = c.replaceAll("^i", "í");
            c = c.replaceAll("^o", "ó");
            c = c.replaceAll("^u", "ú");
            c = c.replaceAll("^^u", "ü");
            c = c.replaceAll("^n", "ñ");

            conjugations[i] = c;
        })

        createDoc();

        savedName = name;
        name = "";
        saved = true;
        conjugations = ['', '', '', '', '', ''];
    }
</script>

<main>
    
    <div class="content center">
        <h1>New Word</h1>
        {#if saved}
            <p>{savedName} was saved</p>
        {/if}
        <input bind:value={name} placeholder="Verb">
        <br>
        {#each [0, 1, 2, 3, 4, 5] as c}
            <input bind:value={conjugations[c]} placeholder={forms[c]}>
            <br>
        {/each}
        <button on:click={createVerb}>Create</button>
    </div>

</main>

<style>
	main {
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		font-family: 'Raleway', sans-serif;
	}

	.center {
		text-align: center;
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