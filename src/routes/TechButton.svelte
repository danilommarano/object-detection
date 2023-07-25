<script lang="ts">
  export let techName: string;
  export let logoSrc: string;
</script>

<div class="flex items-center justify-between py-1 px-2 rounded-full bg-white shadow-sm cursor-poniter hover:bg-gradient-to-b hover:from-white hover:to-gray-100">
  <img class="h-4 w-4 mr-2" src={logoSrc} alt="Technology Logo" />
  <span class="font-semibold text-sm ">{techName}</span>
</div>
