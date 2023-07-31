<script>
    import Logo from '../components/Logo.svelte';
    import Tabs from '../components/Tabs.svelte';
    import Tab from '../components/Tab.svelte';

  const links = [
    { label: 'Análises', emoji: '📉' },
    { label: 'Sistemas', emoji: '🖥️' },
    { label: 'Modelos', emoji: '🧠' },
    { label: 'Blog', emoji: '📝' },
  ]

</script>

<nav class='fixed flex justify-center border-b h-16 w-full bg-white'>
    <div class='container flex justify-between items-center h-full'>
    <Logo/>
    <Tabs class='flex items-end gap-4 h-full'>
      {#each links as link}
        <Tab
            class="flex items-center p-3 border-b gap-2 font-semibold text-gray-500 border-transparent hover:border-gray-800 hover:text-gray-800 active:border-gray-800 selection:border-gray-800"
            on:click={() => alert(link.label)}
        >
          <p>{link.emoji}</p>
          <p>{link.label}</p>
        </Tab>
      {/each}
    </Tabs>
    </div>
</nav>
    
