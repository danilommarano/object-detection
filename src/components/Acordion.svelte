<script lang="ts" context="module">
    export interface ObjectDetectionClass {
      name: string;
      id: number;
      displayName: string;
    }
  
    export interface ObjectsData {
      [key: string]: ObjectDetectionClass[];
    }
  
    export let objects: ObjectsData;
  
    let activeSection: number | null = null;
  
    function toggleSection(index: number) {
      activeSection = activeSection === index ? null : index;
    }
  
    // Helper function to handle keyboard events
    function handleAccordionKeyboardEvent(event: KeyboardEvent, index: number) {
      if (event.key === "Enter" || event.key === " ") {
        toggleSection(index);
      }
    }
  </script>
  
  
  {#each Object.keys(objects) as category, index}
    <button
      class="accordion-section p-4 border-t"
      class:selected="{activeSection === index}"
      on:click={() => toggleSection(index)}
      tabindex="0" 
      on:keydown={(e) => handleAccordionKeyboardEvent(e, index)}
    >
      <h3 class="text-lg font-semibold">2{category}</h3>
      <div
        class="accordion-content mt-2"
        class:hidden="{activeSection !== index}"
      >
        <ul>
          {#each objects[category] as object}
            <li>{object.displayName}</li>
          {/each}
        </ul>
      </div>
    </button>
  {/each}
  