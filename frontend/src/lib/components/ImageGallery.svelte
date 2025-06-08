<script lang="ts">
  import { createEventDispatcher, onMount, onDestroy } from 'svelte';

  // Props: The list of all images for the adventure and the index to start with
  export let images: { id: string; image: string; is_primary: boolean }[] = [];
  export let startIndex: number = 0;

  let currentIndex = startIndex;

  const dispatch = createEventDispatcher();

  function close() {
    dispatch('close');
  }

  function nextImage() {
    currentIndex = (currentIndex + 1) % images.length;
  }

  function prevImage() {
    // The modulo operator in JS can be negative, so we add images.length
    currentIndex = (currentIndex - 1 + images.length) % images.length;
  }

  // Handle keyboard navigation (Escape, ArrowLeft, ArrowRight)
  function handleKeydown(event: KeyboardEvent) {
    if (event.key === 'Escape') {
      close();
    } else if (event.key === 'ArrowRight') {
      nextImage();
    } else if (event.key === 'ArrowLeft') {
      prevImage();
    }
  }

  onMount(() => {
    window.addEventListener('keydown', handleKeydown);
  });

  onDestroy(() => {
    window.removeEventListener('keydown', handleKeydown);
  });
</script>

<div
  class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50"
  on:click={close}
  role="dialog"
  aria-modal="true"
>
  <div class="relative max-w-5xl w-full max-h-[90vh] p-4" on:click|stopPropagation>
    <button
      class="absolute -top-0 -right-0 md:top-2 md:right-2 btn btn-circle btn-sm btn-ghost text-white text-2xl z-20"
      on:click={close}
      aria-label="Close"
    >
      ✕
    </button>

    <div class="relative w-full h-full flex items-center justify-center">
      {#if images.length > 0}
        <img
          src={images[currentIndex].image}
          alt="Enlarged adventure view"
          class="max-h-[85vh] w-auto object-contain rounded-lg shadow-2xl"
        />
      {/if}
    </div>

    {#if images.length > 1}
      <button
        class="absolute top-1/2 left-2 md:left-4 -translate-y-1/2 btn btn-circle btn-ghost text-white text-4xl opacity-70 hover:opacity-100"
        on:click|stopPropagation={prevImage}
        aria-label="Previous image"
      >
        &#10094;
      </button>

      <button
        class="absolute top-1/2 right-2 md:right-4 -translate-y-1/2 btn btn-circle btn-ghost text-white text-4xl opacity-70 hover:opacity-100"
        on:click|stopPropagation={nextImage}
        aria-label="Next image"
      >
        &#10095;
      </button>
    {/if}

    <div class="absolute bottom-4 left-1/2 -translate-x-1/2 bg-black bg-opacity-60 text-white text-sm px-3 py-1 rounded-full">
      {currentIndex + 1} / {images.length}
    </div>
  </div>
</div>
