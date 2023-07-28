<!-- CopyButton.svelte -->
<script lang="ts">
  export let url: string;

  function copyUrl() {
    const textArea = document.createElement("textarea");
    textArea.value = url || window.location.href;
    document.body.appendChild(textArea);
    textArea.select();
    document.execCommand("copy");
    document.body.removeChild(textArea);
    alert("URL copied to clipboard!");
  }
</script>

<button class="flex items-center space-x-1 px-4 py-2 font-semibold cursor-pointer" on:click={() => copyUrl()}>
  <img class="w-5 h-5 text-gray-400" src="/material-symbols-content-copy-outline-rounded.svg" alt="Copy Icon" />
</button>
