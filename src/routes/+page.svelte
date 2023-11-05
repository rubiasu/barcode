<script>
    import Barcode from "$lib/Barcode.svelte";

    let adding = false;
    let removing = false;
    let newBarcode = '';
    let newLast = '';
    let newFirst ='';
    let newSection = '';
    let filter = 'All';
    let search = '';

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
        codes = [...codes, {"last": newLast, "first": newFirst, "section": newSection, "barcode": newBarcode}]
        addFlight(newSection);
        newBarcode = '';
        newLast = '';
        newFirst = '';
        newSection = '';
        adding = false;
        sortBarcodes();
        console.log('sort')
    }

    /**
	 * @param {string} newFlight
	 */
    function addFlight(newFlight) {
        if (!flights.includes(newFlight)) {
            flights = [...flights, newFlight];
        }
        flights.sort();
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
        const jsonData = JSON.stringify({ids: codes, sections: flights}, null, 2);
        const blob = new Blob([jsonData], { type: 'application/json' });
        const url = URL.createObjectURL(blob);

        const a = document.createElement('a');     
        a.href = url;
        a.download = 'codes.json';
        a.click();

        URL.revokeObjectURL(url);
    }

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

    function demo() {
        codes = [{
        "last": "Doe",
        "first": "John",
        "section": "Nowhere",
        "barcode": "Long List of Numbers and Letters"
        }]
        flights = ["All"]
    }

    /**
	 * @param {number} index
	 */
    function removeBarcode(index) {
        codes = codes.filter((_, i) => i !== index);
        removing = false;
    }

    function sortBarcodes() {
        codes.sort((a, b) => {
            // Compare by last name
            if (a.last < b.last) return -1;
            if (a.last > b.last) return 1;
            
            // If last names are equal, compare by first name
            return a.first.localeCompare(b.first);
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
    {:else}
    <button on:click={demo}>Demo</button>
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
                    <h2>{code.last}, {code.first}</h2>
                    {#if removing}
                        <button on:click={() => removeBarcode(index)}>Remove</button>
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
        <label for="last">Enter Last Name</label>
        <input id="last" bind:value={newLast} placeholder="Enter Last Name">
        <label for="first">Enter First Name</label>
        <input id="first" bind:value={newFirst} placeholder="Enter First Name">
        <label for="section">Enter Section</label>
        <input id="section" list="flights" bind:value={newSection} placeholder="Enter Section">
        <datalist id="flights">
            {#each flights as flight}
                <option value={flight}>{flight}</option>
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