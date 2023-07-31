<script lang="ts">
  import TechButton from '../components/TechButton.svelte';
  import BreadCrumb from '../components/BreadCrumb.svelte';
  import Logo from '../components/Logo.svelte';
  import Tabs from '../components/Tabs.svelte';
  import Tab from '../components/Tab.svelte';
  import Navbar from '../components/Navbar.svelte';
  import BadgesList from '../components/BadgesList.svelte';
    import ObjectDetectionCamera from '../components/ObjectDetectionCamera.svelte';
  
  const tabs = [
    { label: 'Modelo', emoji: '📦' },
    { label: 'Artigo', emoji: '📄' },
  ];

  let activePanel:string = tabs[0]['label'];

    function handleTabClick(tab_label:string) {
      activePanel = tab_label;
    }
  </script>

  <main class='flex justify-center text-gray-800'>
    <Navbar>
      <Logo/>
      <div>
          <div class='flex gap-8 text-lg font-semibold'>
              <a class=''>Análises</a>
              <a class=''>Sistemas</a>
              <a class=''>Websites</a>
              <a class=''>Modelos</a>
              <a class=''>Blog</a>
          </div>
      </div>
    </Navbar>
    <div class='w-full h-screen mt-16'>
        <div class='flex justify-center items-end h-48 bg-gradient-to-b from-white to-gray-50'>
          <div class='flex flex-col gap-6 container text-gray-500'>
              <div class='flex flex-col gap-3'>
                <div class='flex flex-col'>
                  <BreadCrumb items={["Computação", "Inteligência Artificial"]} />
                  <h1 class='font-bold text-4xl text-gray-600'>Detecção de objetos usando o COCO-SSD</h1>
                  </div>
                  <BadgesList>
                    <TechButton techName={'TypeScript'} logoSrc={'vscode-icons-file-type-typescript-official.svg'} />
                    <TechButton techName={'Svelte'} logoSrc={'vscode-icons-file-type-svelte.svg'} />
                    <TechButton techName={'Tailwind'} logoSrc={'devicon-tailwindcss.svg'} />
                    <TechButton techName={'Tensorflow'} logoSrc={'devicon-tensorflow.svg'} />
                    <TechButton techName={'Python'} logoSrc={'vscode-icons-file-type-python.svg'} />
                  </BadgesList>
                </div>
                  <Tabs>
                    {#each tabs as tab}
                      <Tab on:click={() => handleTabClick(tab.label)}>
                        <p>{tab.emoji}</p>
                        <p>{tab.label}</p>
                      </Tab>
                    {/each}
                  </Tabs>
            </div>
        </div>

        <div class='flex justify-center'>
          <div class='container w-full pt-4'>
              {#if activePanel === tabs[0]['label']}
              <ObjectDetectionCamera />
              {/if}


              {#if activePanel === tabs[1]['label']}
              teste
              {/if}
            </div>
        </div>
    </div>
  </main>
  