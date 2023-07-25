<!-- Breadcrumb.svelte -->
<script lang="ts">
  export let items: string[] = [];
</script>

<div class="flex items-center space-x-2 text-2xl font-semibold">
  {#each items as item, index}
    <span class="text-gray-400 last:text-gray-800">{item}</span>
    {#if index !== items.length - 1}
      <span>/</span>
    {/if}
  {/each}
</div>