<script>
  import PlainText from '$lib/components/PlainText.svelte';
  import { fetchJSON } from '$lib/util';
  import PrimaryButton from '$lib/components/PrimaryButton.svelte';
  import LoginMenu from '$lib/components/LoginMenu.svelte';
  import Footer from '$lib/components/Footer.svelte';
  import { currentUser, isEditing } from '$lib/stores.js';
  import WebsiteHeader from '$lib/components/WebsiteHeader.svelte';

  export let data;

  // Default CV content following JSON Resume structure
  const DEFAULT_CV = {
    basics: {
      name: 'Your Name',
      label: 'Job Title',
      email: 'email@example.com',
      phone: '(555) 123-4567',
      url: 'https://yourwebsite.com',
      location: {
        city: 'City',
        region: 'ST'
      },
      profiles: [
        { network: 'GitHub', url: 'https://github.com/username' },
        { network: 'LinkedIn', url: 'https://linkedin.com/in/username' }
      ]
    },
    skills: [
      { name: 'Languages', keywords: ['JavaScript', 'Python', 'TypeScript'] },
      { name: 'Frameworks', keywords: ['SvelteKit', 'React', 'Node.js'] }
    ],
    work: [
      {
        position: 'Job Title',
        name: 'Company Name',
        location: 'City, ST',
        startDate: 'June 2022',
        endDate: 'Present',
        highlights: [
          'Accomplished X as measured by Y by doing Z',
          'Improved performance by X% through implementation of Y',
          'Led team of N engineers to deliver Z'
        ]
      }
    ],
    projects: [
      {
        name: 'Project Name',
        url: 'https://project.com',
        highlights: ['Built a tool that does X for Y users', 'Technologies used: A, B, C']
      }
    ],
    education: [
      {
        institution: 'University Name',
        area: 'Computer Science',
        studyType: 'BS',
        endDate: 'May 2020'
      }
    ]
  };

  let cv;
  let showUserMenu;

  function initOrReset() {
    $currentUser = data.currentUser;
    cv = data.page?.cv || JSON.parse(JSON.stringify(DEFAULT_CV));
    $isEditing = false;
  }

  function toggleEdit() {
    $isEditing = true;
    showUserMenu = false;
  }

  async function savePage() {
    try {
      if ($currentUser) {
        await fetchJSON('POST', '/api/save-page', {
          pageId: 'cv',
          page: { cv }
        });
      }
      $isEditing = false;
    } catch (err) {
      console.error(err);
      alert('There was an error. Please try again.');
    }
  }

  initOrReset();
</script>

<svelte:head>
  <title>{cv.basics.name} - CV</title>
  <meta name="description" content="CV for {cv.basics.name}" />
</svelte:head>

<WebsiteHeader bind:showUserMenu on:cancel={initOrReset} on:save={savePage}>
  <PrimaryButton on:click={toggleEdit}>Edit CV</PrimaryButton>
  <LoginMenu />
</WebsiteHeader>

<main class="max-w-screen-md mx-auto px-6 py-12 print:py-0 print:px-0">
  <!-- Header -->
  <header class="text-center mb-6 border-b-2 border-gray-900 pb-4">
    <h1 class="text-3xl font-bold">
      <PlainText bind:content={cv.basics.name} />
    </h1>
    <p class="text-sm text-gray-600 mt-2">
      <PlainText bind:content={cv.basics.email} />
      {#if cv.basics.url}
        <span class="mx-2">|</span>
        <PlainText bind:content={cv.basics.url} />
      {/if}
      {#if cv.basics.profiles?.length}
        {#each cv.basics.profiles as profile}
          <span class="mx-2">|</span>
          <PlainText bind:content={profile.url} />
        {/each}
      {/if}
    </p>
  </header>

  <!-- Skills -->
  <section class="mb-6">
    <h2 class="text-lg font-bold border-b border-gray-400 mb-2">Skills</h2>
    {#each cv.skills as skill, i}
      <p class="text-sm">
        <strong><PlainText bind:content={cv.skills[i].name} />:</strong>
        {skill.keywords.join(', ')}
      </p>
    {/each}
  </section>

  <!-- Experience -->
  <section class="mb-6">
    <h2 class="text-lg font-bold border-b border-gray-400 mb-2">Experience</h2>
    {#each cv.work as job}
      <div class="mb-4">
        <div class="flex justify-between items-baseline">
          <p class="font-semibold">
            <PlainText bind:content={job.position} />, <PlainText bind:content={job.name} /> – <PlainText
              bind:content={job.location}
            />
          </p>
          <p class="text-sm text-gray-600">
            <PlainText bind:content={job.startDate} /> – <PlainText bind:content={job.endDate} />
          </p>
        </div>
        <ul class="list-disc list-inside text-sm mt-1 ml-2">
          {#each job.highlights as highlight, i}
            <li><PlainText bind:content={job.highlights[i]} /></li>
          {/each}
        </ul>
      </div>
    {/each}
  </section>

  <!-- Projects -->
  <section class="mb-6">
    <h2 class="text-lg font-bold border-b border-gray-400 mb-2">Projects</h2>
    {#each cv.projects as project}
      <div class="mb-3">
        <p class="font-semibold">
          <PlainText bind:content={project.name} />
          {#if project.url}
            <span class="font-normal text-sm text-gray-600 ml-2">
              <PlainText bind:content={project.url} />
            </span>
          {/if}
        </p>
        <ul class="list-disc list-inside text-sm mt-1 ml-2">
          {#each project.highlights as highlight, i}
            <li><PlainText bind:content={project.highlights[i]} /></li>
          {/each}
        </ul>
      </div>
    {/each}
  </section>

  <!-- Education -->
  <section class="mb-6">
    <h2 class="text-lg font-bold border-b border-gray-400 mb-2">Education</h2>
    {#each cv.education as edu}
      <div class="flex justify-between items-baseline">
        <p>
          <span class="font-semibold"><PlainText bind:content={edu.institution} /></span>
          – <PlainText bind:content={edu.studyType} /> in <PlainText bind:content={edu.area} />
        </p>
        <p class="text-sm text-gray-600">
          <PlainText bind:content={edu.endDate} />
        </p>
      </div>
    {/each}
  </section>
</main>

<Footer counter="/cv" />

<style>
  @media print {
    :global(body) {
      font-size: 11pt;
    }
  }
</style>
