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
    let definition = "";
    let showUnverifiedVerbs = false;
    let unverifiedVerbs = [];

    let verbRef = createRef();

    async function loadUnverifiedVerbs() {
        showUnverifiedVerbs = true;
        let l_ref = await db.collection('verbs').where('verified', '==', false).get();
        let l_data_arr = [];

        l_ref.forEach((e, i) => {
            l_data_arr = [...l_data_arr, e.data()]
        });

        console.log(unverifiedVerbs);

        unverifiedVerbs = l_data_arr;
    }

    async function createRef() {
        let l_ref = await db.collection('verbs');
        return l_ref;
    }

    async function createDoc() {
        const data = {
            name: name.toLowerCase(),
            definition: definition.toLowerCase(),
            indicPresent: conjugations,
            verified: false
        };

        const res = await db.collection('verbs').doc(`${name}`).set(data);
    }

    async function createVerb() {
        name = name.toLowerCase();
        name = name.replaceAll("^a", "á");
        name = name.replaceAll("^e", "é");
        name = name.replaceAll("^i", "í");
        name = name.replaceAll("^o", "ó");
        name = name.replaceAll("^u", "ú");
        name = name.replaceAll("^^u", "ü");
        name = name.replaceAll("^n", "ñ");

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

        savedName = name.toLowerCase();
        name = "";
        saved = true;
        conjugations = ['', '', '', '', '', ''];
        definition = "";
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
        <br>
        <input bind:value={definition} placeholder="Defition: to ____">
        <br>
        {#each [0, 1, 2, 3, 4, 5] as c}
            <input bind:value={conjugations[c]} placeholder={forms[c]}>
            <br>
        {/each}
        <button class="button-create" on:click={createVerb}>Create</button>
        <br>
        {#if !showUnverifiedVerbs}
            <button on:click={loadUnverifiedVerbs}>Load Unverified Verbs</button>
            <p>Unverified verbs may include sensitive information</p>
        {:else}
            {#await unverifiedVerbs}
                <p>Loading</p>
            {:then unverifiedVerbs} 
                {#each unverifiedVerbs as v}
                    <p>{v.name}</p>
                {/each}
            {/await}
        {/if}
    </div>

</main>