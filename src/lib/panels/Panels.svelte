<script>
    import { onMount, onDestroy, setContext, getContext } from 'svelte';
    import { panels} from './store';
    export let input_direction  = "horizontal"
    export let org_width_distribution = [] // 0-1, make sure it adds to exactly 1
    export let id = 0
    let container = [0,0]
    setContext("panelWrapperID",id)

    function handleResize(event) {
        let local_panel = $panels[id]
        if (!local_panel.isResizing) return;

        try {
            const isHorizontal = input_direction === "horizontal";
            const delta = isHorizontal ? event.clientX - local_panel.startX : event.clientY - local_panel.startY;
            const containerSize = isHorizontal ? container[0] : container[1];
            const newSizes = calculateNewSizes(delta, containerSize, isHorizontal,local_panel);

            if (newSizes) {
                
                let result = $panels[id].panels.find(panel => {
                    return panel.id == local_panel.panelBeingResized
                })
                
                let result2 = $panels[id].panels.find(panel => {
                    return panel.id == local_panel.panelBeingResized +1
                })
                result.size = newSizes.newSizePrimary
                result2.size = newSizes.newSizeSecondary
                $panels = $panels
                if (isHorizontal) {
                    local_panel.startX = event.clientX;
                } else {
                    local_panel.startY = event.clientY;
                }
            }
        } catch (error) {
            console.error("Error during resize:", error);
        }
    }

    function calculateNewSizes(delta, containerSize, isHorizontal,local_panel) {
        let panelBeingResized = local_panel.panelBeingResized
        let minSizePrimary = $panels[id].panels[panelBeingResized].minSize
        let minSizeSecondary = $panels[id].panels[panelBeingResized +1].minSize
        const currentSize = $panels[id].panels.map(panel => panel.size);
        const newSizePrimary = currentSize[panelBeingResized] + delta / containerSize;
        const newSizeSecondary = currentSize[panelBeingResized + 1] - delta / containerSize;

        if (newSizePrimary >= minSizePrimary && newSizeSecondary >= minSizeSecondary) {
            console.log("-------------------------")
            console.log("NewSizePrimary",newSizePrimary)
            console.log("minSizePrimary",minSizePrimary)
            console.log("newSizeSecondary",newSizeSecondary)
            console.log("minSizeSecondary",minSizeSecondary)
            console.log(local_panel.startX )
            return { newSizePrimary, newSizeSecondary };
        }

        return null;
    }

 



    function stopResize() {
        $panels[id].isResizing = false
    }

    class PanelWrapper{
        constructor(direction = "horizontal", panels=[],id = null){
            this.direction = direction
            this.panels = panels
            this.id = id
        }
    }

    class Panel{
        constructor(size=0.1,id){
            this.size = size
            this.id = id
        }
    }

    function init(){
        let element = document.querySelector(`[data-panel-wrapper-id="${id}"]`)
        container[0] = element.clientWidth
        container[1] = element.clientHeight
        if(org_width_distribution.length == 0){
            org_width_distribution = new Array(org_width_distribution.length).fill(1 / org_width_distribution.length)
        }
        let list_of_panels = new Array
        let index = 0
        org_width_distribution.forEach(size => {
            let id // in order to force it to be a local reference
            let panel = new Panel(size,id = index)
            list_of_panels.push(panel)
            index += 1

        });
        let panelwrapper = new PanelWrapper(input_direction,list_of_panels,id)
        $panels[id] = panelwrapper
    }

    onMount(() => {
        console.log("mount")
        init()
        document.addEventListener("mouseup", stopResize);
        document.addEventListener("mousemove", handleResize);
        
    });

    onDestroy(() => {
        delete $panels[id]
        document.removeEventListener("mouseup", stopResize);
        document.removeEventListener("mousemove", handleResize);
    });
</script>
<div data-panel-wrapper-id={id} class="panel-container  w-full flex-col relative h-full">

    <slot></slot>
</div>
