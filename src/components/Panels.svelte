<!-- Panels.svelte -->
<script lang="ts">
  export let activeTab = 0;
  console.log(activeTab)
</script>

<div>
  {#if activeTab === 0}
    <div>Panel 1 content</div>
  {/if}

  {#if activeTab === 1}
    <div>Panel 2 content</div>
  {/if}

  {#if activeTab === 2}
    <div>Panel 3 content</div>
  {/if}
</div>
