<script>
    import Barcode from "$lib/Barcode.svelte";

    let adding = false;
    let removing = false;
    let newBarcode = '';
    let newName = '';
    let newSection = '';
    let filter = 'All';

    /**
	 * @type {any[]}
	 */
    let codes = [];

    /**
     * @type {any[]}
     */
    let flights = [];


    function enterBarcode() {
        adding = true;
    }

    function addBarcode() {
        codes = [...codes, {"name": newName, "section": newSection, "barcode": newBarcode}]
        addFlight(newSection);
        newBarcode = '';
        newName = '';
        newSection = '';
        adding = false;
    }

    /**
	 * @param {string} newFlight
	 */
    function addFlight(newFlight) {
        if (!flights.includes(newFlight)) {
            flights = [...flights, newFlight];
        }
    }

    

    /**
	 * Handle file upload.
     * 
     * @param {Event} event - The input change event
	 */
    function loadBarcodes(event) {
        // @ts-ignore
        const file = event.target.files[0];
        if (file && file.type === "application/json") {
        const reader = new FileReader();
        reader.onload = () => {
                const result = reader.result;
                if (typeof result === 'string') {
                    try {
                        const data = JSON.parse(result);
                        codes = data.ids;
                        flights = data.sections;
                    } catch (error) {
                        console.error("Error parsing JSON file:", error);
                    }
                } else {
                    console.error("Unexpected file content:", result);
                }
            };
        reader.readAsText(file);
        console.log(JSON.stringify(codes));
        } else {
        console.error("Invalid file type. Please upload a JSON file.");
        }
    } 

    function saveBarcodes() {
        const jsonData = JSON.stringify({ids: codes, flights: flights}, null, 2);
        const blob = new Blob([jsonData], { type: 'application/json' });
        const url = URL.createObjectURL(blob);

        const a = document.createElement('a');     
        a.href = url;
        a.download = 'codes.json';
        a.click();

        URL.revokeObjectURL(url);
    }

    // function filterFlight() {
    //     console.log(filter)
    // }

    /**
	 * @param {string} text
	 */
    function copyBarcode(text) {
        navigator.clipboard.writeText(text).then(function(){
            console.log('Copying to clipboard was successful!');
        }, function(err) {
            console.log('Could not copy text', err);
        });
    }
    
  </script>

  <input type="file" accept=".json" on:change={loadBarcodes}/>
  {#if codes.length > 0}
    <button on:click={saveBarcodes}>Save Barcodes</button>
    <button on:click={enterBarcode}>Add Barcode</button>
    <button on:click={() => removing = !removing}>Remove Barcode</button>
    <label for="filter">Filter by section</label>
    <select id="filter" bind:value={filter}>
        {#each flights as flight}
                <option value={flight}>{flight}</option>
        {/each}
    </select>
  {/if}
  <section>
    {#if codes.length == 0}
        <h1>Load Barcodes</h1>
    {:else}
        {#each codes as code, index }
            {#if (code.section == filter || filter == 'All')}
                <div>
                    {#if code.barcode != ''}
                        <Barcode barcodeValue={code.barcode} on:click={() => copyBarcode(code.barcode)} />
                    {:else}
                        <Barcode barcodeValue=' ' />
                    {/if}
                    <h2>{code.name}</h2>
                    {#if removing}
                        <button on:click={() => {codes.splice(index, 1); removing = false;}}>Remove</button>
                    {:else}
                        <button on:click={() => copyBarcode(code.barcode)}>Copy</button>
                    {/if}
                </div>
            {/if}
        {/each}
    {/if}
</section>

{#if adding}
    <aside>
        <label for="barcode">Enter Barcode</label>
        <input id="barcode" bind:value={newBarcode} placeholder="Enter Barcode">
        <label for="name">Enter Name</label>
        <input id="name" bind:value={newName} placeholder="Enter Name">
        <label for="section">Enter Section</label>
        <input id="section" list="flights" bind:value={newSection} placeholder="Enter Section">
        <datalist id="flights">
            {#each flights as flight}
                <option value="flight">{flight}</option>
            {/each}
        </datalist>
        <button on:click={addBarcode}>Submit</button>
    </aside>
{/if}

  <style>
    section {
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        grid-template-rows: auto;
        gap: 1rem;
        max-width: 100dvw;
    }
    div {
        display: flex;
        flex-direction: column;
        align-items: center;
        grid-column: span 4;
    }

    @media (max-width: 1200px) {
        div {
            grid-column: span 6;
        }
    }

    @media (max-width: 800px) {
        div {
            grid-column: span 12;
        }
    }

  </style>