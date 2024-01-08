<script>
    import PanelsOrg from './lib/panels/Panels_org.svelte';
    import Panels from './lib/panels/Panels.svelte';
    import Handle from './lib/panels/Handle.svelte';
    import ResizablePanel from './lib/panels/ResizablePanel.svelte';

</script>

<main>
  <div class="h-screen  p-20 w-screen">
    <!-- <PanelsOrg numPanels={1}>
      
    </PanelsOrg> -->

        <Panels id={0} input_direction="horizontal" org_width_distribution={[0.1,0.7,0.2]}  >
          <ResizablePanel minW={0.1} panelID={0}>
            <p>panel 1</p>
          </ResizablePanel>
          
          <Handle forPanel={0}></Handle>

    
          <ResizablePanel minW={0.1} panelID={1}>
              <Panels id={1} input_direction="vertical"  org_width_distribution={[0.9,0.1]}  >
                <ResizablePanel minW={0.1} panelID={0}>
                  <p>panel 1</p>
                </ResizablePanel>
                
                <Handle forPanel={0}></Handle>
                <ResizablePanel minW={0.1} panelID={1}>
                  <p>panel 2</p>
                </ResizablePanel>
              </Panels>
          </ResizablePanel>
          
          <Handle withHandle={false} forPanel={1}></Handle>

          <ResizablePanel minW={0.1} panelID={2}>
            <p>panel 2</p>
          </ResizablePanel>
        </Panels>

    
    
    
  </div>
</main>

<style>
  
</style>
