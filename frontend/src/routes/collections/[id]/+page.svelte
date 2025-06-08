<script lang="ts">
  import type { PageData } from './$types';
  import type { Adventure, Collection } from '$lib/types';
  
  // Assuming you created ImageGallery.svelte in this path
  import ImageGallery from '$lib/components/ImageGallery.svelte'; 

  export let data: PageData;

  // Thanks to our fix in +page.server.ts, we now correctly receive a 'collection' prop
  let collection: Collection | null = data.props.collection;

  // Page-level state to manage the gallery
  let isGalleryOpen = false;
  let galleryImages: Adventure['images'] = [];
  let galleryStartIndex = 0;

  /**
   * Opens the gallery with the specified images and starting index.
   * @param {Adventure['images']} images - The array of images for an adventure.
   * @param {number} index - The index of the image that was clicked.
   */
  function openGallery(images: Adventure['images'], index: number) {
    galleryImages = images;
    galleryStartIndex = index;
    isGalleryOpen = true;
  }

  /**
   * Closes the image gallery.
   */
  function closeGallery() {
    isGalleryOpen = false;
  }
</script>

<div class="container mx-auto p-4 md:p-8">
  {#if collection}
    <div class="prose max-w-none mb-8">
      <h1>{collection.name}</h1>
      <p>{collection.description || 'No description for this collection.'}</p>
    </div>

    <h2 class="text-2xl font-bold mb-6 border-b pb-2">Linked Adventures</h2>

    {#if collection.adventures && collection.adventures.length > 0}
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        
        {#each collection.adventures as adventure (adventure.id)}
          <div class="card card-compact bg-base-100 shadow-lg hover:shadow-2xl transition-shadow">
            
            {#if adventure.images && adventure.images.length > 0}
              <figure class="relative">
                <img
                  src={adventure.images[0].image}
                  alt="Primary image for {adventure.name}"
                  class="w-full h-48 object-cover cursor-pointer hover:opacity-90 transition-opacity"
                  on:click={() => openGallery(adventure.images, 0)}
                  on:keydown={(e) => e.key === 'Enter' && openGallery(adventure.images, 0)}
                  tabindex="0"
                />
                {#if adventure.images.length > 1}
                   <div class="absolute bottom-2 right-2 badge badge-neutral">
                     {adventure.images.length} photos
                   </div>
                {/if}
              </figure>
            {/if}

            <div class="card-body">
              <h3 class="card-title">{adventure.name}</h3>
              <p class="text-sm opacity-70">{adventure.location || 'No location specified'}</p>
            </div>
          </div>
        {/each}

      </div>
    {:else}
      <p>This collection has no linked adventures yet.</p>
    {/if}
  {:else}
    <p>Loading collection details...</p>
  {/if}
</div>

{#if isGalleryOpen}
  <ImageGallery
    images={galleryImages}
    startIndex={galleryStartIndex}
    on:close={closeGallery}
  />
{/if}
