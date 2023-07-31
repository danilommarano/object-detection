<script lang="ts">
  export let techName: string;
  export let logoSrc: string;
</script>

<button class="flex h-6 items-center gap-1/2 px-2 w-fit rounded-full border bg-white shadow-sm cursor-poniter hover:bg-gradient-to-b hover:from-white hover:to-gray-100">
  <img class="h-4 w-4 mr-2" src={logoSrc} alt="Technology Logo" />
  <span class="font-semibold text-sm ">{techName}</span>
</button>
