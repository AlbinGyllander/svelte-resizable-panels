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
    let nested = false
    $:$panels,unpackPanels()
    function unpackPanels(){
        if(Object.keys($panels).length > 0 ){
            if($panels[panelWrapperID] !== undefined){
                panels_length = $panels[panelWrapperID].panels.length
                loaded_panel_sizes = $panels[panelWrapperID].panels.map(panel => panel.size);
                direction = $panels[panelWrapperID].direction
                nested = $panels[panelWrapperID].nested
                loaded = true
                if(firstIter){
                    $panels[panelWrapperID].panels[index].minSize = minW
                    firstIter = false
                }
                
            }
        }
    }
    let panelElement



    $: calculateStyle = direction === "vertical"
    ? `height: ${loaded_panel_sizes[index] * 100}%; top: ${index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%`
    : `width: ${loaded_panel_sizes[index] * 100}%; left: ${index > 0 ? loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 : 0}%`;
    
</script>

{#if loaded}
  <div 
    bind:this={panelElement}
    on:keydown={()=>{}}
    data-panel
    role="button"
    tabindex="0"
    class="absolute h-full w-full {nested ?'' :'border'} border-gray-200 flex justify-center items-center"
    style={calculateStyle}>
    <slot></slot>
  </div>
{/if}