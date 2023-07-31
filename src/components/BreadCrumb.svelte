<!-- Breadcrumb.svelte -->
<script lang="ts">
  export let items: string[] = [];
</script>

<div class="flex items-center space-x-2 font-semibold text-gray-400">
  {#each items as item, index}
    <span class="">{item}</span>
    {#if index !== items.length - 1}
      <span>/</span>
    {/if}
  {/each}
</div>