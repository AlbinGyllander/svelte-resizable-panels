<script>
    import { getContext ,onMount} from "svelte";
    import {  panels} from "./store";
    export let panelID
    export let minW = 0.1;
    let index = panelID
    let panelWrapperID = getContext("panelWrapperID")
    let loaded = false
    let loaded_panel_sizes = []
    let panels_length = null
    let direction
    let firstIter = true
    $:$panels,unpackPanels()
    function unpackPanels(){
        if(Object.keys($panels).length > 0 ){
            if($panels[panelWrapperID] !== undefined){
                panels_length = $panels[panelWrapperID].panels.length
                loaded_panel_sizes = $panels[panelWrapperID].panels.map(panel => panel.size);
                direction = $panels[panelWrapperID].direction
                loaded = true
                if(firstIter){
                    console.log(minW)
                    $panels[panelWrapperID].panels[index].minSize = minW
                    firstIter = false
                }
                
            }
        }
    }
    let panelElement



    function findAdjacentPanel(sibling, direction) {
        while (sibling && !sibling.matches('[data-panel]')) {
        sibling = direction === 'prev' ? sibling.previousElementSibling : sibling.nextElementSibling;
        }
        return sibling;
    }

    function updateBorders() {
        const prevSibling = findAdjacentPanel(panelElement.previousElementSibling, 'prev');
        const nextSibling = findAdjacentPanel(panelElement.nextElementSibling, 'next');
        
        const hasPrevPanel = !!prevSibling;
        const hasNextPanel = !!nextSibling;

        // Update left and right borders
        panelElement.style.borderLeft = hasPrevPanel ? 'none' : '';
        panelElement.style.borderRight = hasNextPanel ? 'none' : '';

        // Update top and bottom borders
        // For vertical layouts, the top border is affected by the previous panel and the bottom by the next
        if (direction === 'vertical') {
        panelElement.style.borderTop = hasPrevPanel ? 'none' : '';
        panelElement.style.borderBottom = hasNextPanel ? 'none' : '';
        }
        // For horizontal layouts, you might need additional logic to determine when to set or remove top and bottom borders
        // This will depend on your specific layout and how panels are structured vertically
        else {
        // Placeholder for your horizontal layout logic
        // You might check for the presence of another row of panels, etc.
        panelElement.style.borderTop = hasPrevPanel ? 'none' : '';
        panelElement.style.borderBottom = hasNextPanel ? 'none' : '';
        }
    }

    function borderClasses() {
        if (direction === "vertical") {
        return `border-r border-l ${index == 0 ? "border-t" : ""} ${index == loaded_panel_sizes.length - 1 ? "border-b" : ""}`;
        } else {
        return `border-t border-b ${index == 0 ? "border-l" : ""} ${index == panels_length - 1 ? "border-r" : ""}`;
        }
    }
    $: calculateStyle = direction === "vertical"
    ? `height: ${loaded_panel_sizes[index] * 100}%; top: ${index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%`
    : `width: ${loaded_panel_sizes[index] * 100}%; left: ${index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%`;
    
</script>
<!-- {#if loaded==true}
    {#if direction == "vertical"}
    
    <div 
        data-panel
        class=" w-full  absolute {index==0 ? "border-t":""} {index == loaded_panel_sizes.length - 1 ? "border-b":""}  border-r border-l border-gray-200 flex justify-center items-center"
        style="height: {loaded_panel_sizes[index] * 100}%; top: {index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%;">
        <slot></slot>      

    </div>
    {:else}
    <div 
        bind:this={panelElement}
        on:keydown={()=>{}}
        on:click={(e)=>{updateBorders()}}
        data-panel
        class=" h-full absolute {index==0 ? "border-l":""} {index == panels_length- 1 ? "border-r":""}  border-t border-b border-gray-200 flex justify-center items-center"
        style="width: {loaded_panel_sizes[index] * 100}%; left:{index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%;">
        <slot></slot>  


    </div>
    {/if}     
{/if} -->

{#if loaded}
  <div 
    bind:this={panelElement}
    on:keydown={()=>{}}
    on:click={updateBorders}
    data-panel
    class="absolute h-full w-full {borderClasses()} border-gray-200 flex justify-center items-center"
    style={calculateStyle}>
    <slot></slot>
  </div>
{/if}


<!-- <slot></slot> -->
