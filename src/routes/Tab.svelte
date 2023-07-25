<!-- Tab.svelte -->
<script lang="ts">
  export let active = false;
  export let label: string;
  export let icon: string;
</script>

<button
  class="flex items-center space-x-2 cursor-pointer border-b-2 border-transparent hover:border-gray-300 active:border-gray-800 selection:border-gray-800"
  on:click={() => active = true}
>
  <img src={icon} alt={label} class="w-6 h-6" />
  <span>{label}</span>
</button>
