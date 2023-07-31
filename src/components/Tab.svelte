<!-- Tab.svelte -->
<script lang="ts">
</script>

<button
  class="flex items-center p-3 border-b gap-1 text-gray-500 border-transparent hover:border-gray-800 active:border-gray-800 selection:border-gray-800"
  on:click
  {...$$props}
>
  <slot />
</button>
