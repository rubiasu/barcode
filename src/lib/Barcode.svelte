<script>
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';
    import JsBarcode from 'jsbarcode';
    // @ts-ignore
    import { XMLSerializer, DOMImplementation } from 'xmldom';
  
    let windowWidth = window.innerWidth;
    

    export let barcodeValue = '';
  
    const barcodeStore = writable({ svgText: ' ' });
  
    onMount(() => updateSvg(barcodeValue));
    /**
     * @param {string} value
     */
    function updateSvg(value) {
      let factor = windowWidth;
      switch (true) {
        case windowWidth > 1200:
          factor = 1200;
          break;
        case windowWidth < 400:
          factor = 400;
          break;
      }
      
      const xmlSerializer = new XMLSerializer();
      /** 
       * @type {XMLDocument} 
       */
      // @ts-ignore
      const document = new DOMImplementation().createDocument(
        'http://www.w3.org/1999/xhtml',
        'html',
        null
      )
      const svgNode = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
  
      JsBarcode(svgNode, value, {
        format: 'CODE128',
        xmlDocument: document,
        displayValue: false,
        width: windowWidth / factor,
        height: 50,
      });
  
      const svgText = xmlSerializer.serializeToString(svgNode);
      barcodeStore.set({ svgText });
      
    }
  
   // @ts-ignore
     $: updateSvg(barcodeValue);


  </script>

  <svelte:window on:resize={() => {windowWidth = window.innerWidth; $: updateSvg(barcodeValue)}} />

  <div>
    {@html $barcodeStore.svgText}
  </div>
  