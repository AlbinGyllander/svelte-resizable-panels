<script>
    import { getContext ,setContext} from 'svelte';// This retrieves the value shared from the parent component
    import DragHandle from '../../assets/icons/DragHandle.svelte';
    export let forPanel
    let index = forPanel
    export let withHandle = true
    import {panels} from './store';
    let panelwrapperID = getContext("panelWrapperID")
    let loaded_panel_sizes
    let loaded = false
    let direction
    $:$panels,unpackPanels()
    
    function unpackPanels(){
        if(Object.keys($panels).length > 0 ){
            if($panels[panelwrapperID] !== undefined){
                loaded_panel_sizes = $panels[panelwrapperID].panels.map(panel => panel.size);
                direction = $panels[panelwrapperID].direction
                loaded = true
            }
        }
    }
    function startResize(event, panelIndex) {
        
        if(panelwrapperID == undefined) return
        $panels[panelwrapperID].startX = event.clientX
        $panels[panelwrapperID].startY = event.clientY
        $panels[panelwrapperID].panelBeingResized = panelIndex
        $panels[panelwrapperID].isResizing = true
        event.preventDefault();
    }
</script>
{#if  loaded == true}
    {#if direction == "horizontal"}
    <div  class="cursor-col-resize bg-gray-100 h-full absolute z-50 flex justify-center items-center w-[1px]"
        style="left: {loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 + loaded_panel_sizes[index] * 100 }%;"
        on:mousedown={(event)=>{startResize(event, index)}}>
        
        {#if withHandle}
        <div class="p-1 mr-0.5 bg-gray-100 rounded-lg">
            <DragHandle />
        </div>
        {/if}
    </div>
    {:else}
    <div class=" cursor-row-resize bg-gray-100 w-full absolute z-50 flex justify-center items-center h-[2px]"
        style="top: {loaded_panel_sizes.slice(0, index).reduce((sum, w) => sum + w, 0) * 100 + loaded_panel_sizes[index] * 100 }%;"
        on:mousedown={(event)=>{startResize(event, index)}}>
        {#if withHandle}
        <div class="p-1 tranform rotate-90 bg-gray-100 rounded-lg">
            <DragHandle />
        </div>
        {/if}
    </div>
    {/if}   
{/if}






