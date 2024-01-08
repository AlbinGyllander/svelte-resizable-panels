<script>
    import DragHandle from "../../assets/icons/DragHandle.svelte"

    export let numPanels = 2;
    // let panelWidths = new Array(numPanels).fill(1 / numPanels); // Initial widths for each panel (as a fraction)
    let panelWidths = new Array(numPanels).fill(1 / numPanels); // Initial widths for each panel (even distribution)

    let isResizing = false;
    let startX = 0;
    let panelBeingResized = 0;

    const minWidth = 0.1; // Minimum width for each panel as a fraction of container width

    function startResize(event, panelIndex) {
        isResizing = true;
        startX = event.clientX;
        panelBeingResized = panelIndex;
        event.preventDefault();
    }

    function handleResize(event) {
        if (!isResizing) return;

        const deltaX = event.clientX - startX;
        const containerWidth = window.innerWidth;
        
        const newWidthLeft = panelWidths[panelBeingResized] + deltaX / containerWidth;
        const newWidthRight = panelWidths[panelBeingResized + 1] - deltaX / containerWidth;

        if (
        newWidthLeft >= minWidth && // Check if the new width for the left panel is greater than or equal to the minimum width
        newWidthRight >= minWidth // Check if the new width for the right panel is greater than or equal to the minimum width
        ) {
        // Update panel widths only if within constraints
        panelWidths[panelBeingResized] = newWidthLeft;
        panelWidths[panelBeingResized + 1] = newWidthRight;

        startX = event.clientX;
        }
    }

    function stopResize() {
        isResizing = false;
    }
  
    // Add a global event listener to stop resizing when the mouse button is released anywhere on the document
    document.addEventListener("mouseup", stopResize);
    document.addEventListener("mousemove", handleResize);
  </script>
  

  <div class="panel-container relative h-full">
    {#each panelWidths as width, i}
      <div class=" h-full absolute {i==0 ? "border-l":""} {i == numPanels - 1 ? "border-r":""}  border-t border-b border-gray-200 flex justify-center items-center" style="width: {width * 100}%; left: {i > 0 ? panelWidths.slice(0, i).reduce((sum, w) => sum + w, 0) * 100 : 0}%">
        this content need to by dynamic 
      </div>
      {#if i < numPanels - 1}
        <div
          class=" cursor-col-resize bg-gray-100 h-full absolute z-50 flex justify-center items-center w-0.5"
          data-handle-id={i}
          style="left: {panelWidths.slice(0, i).reduce((sum, w) => sum + w, 0) * 100 + width * 100 - 0.7}%;"
          on:mousedown={() => startResize(event, i)}
        >
            <div class=" p-1  bg-gray-100 rounded-lg">
                <DragHandle/>
            </div>
        </div>
      {/if}
    {/each}
  </div>
  