<script lang="ts">
	import type { Adventure, Checklist, Collection, Lodging, Note, Transportation } from '$lib/types';
	import { onMount, onDestroy } from 'svelte';
	import type { PageData } from './$types';
	import { marked } from 'marked';

	import { t } from 'svelte-i18n';

	// @ts-ignore
	import Calendar from '@event-calendar/core';
	// @ts-ignore
	import TimeGrid from '@event-calendar/time-grid';
	// @ts-ignore
	import DayGrid from '@event-calendar/day-grid';

	import Plus from '~icons/mdi/plus';
	import AdventureCard from '$lib/components/AdventureCard.svelte';
	import AdventureLink from '$lib/components/AdventureLink.svelte';
	import NotFound from '$lib/components/NotFound.svelte';
	import { DefaultMarker, MapLibre, Marker, Popup, LineLayer, GeoJSON } from 'svelte-maplibre';
	import TransportationCard from '$lib/components/TransportationCard.svelte';
	import NoteCard from '$lib/components/NoteCard.svelte';
	import NoteModal from '$lib/components/NoteModal.svelte';

	import {
		groupAdventuresByDate,
		groupNotesByDate,
		groupTransportationsByDate,
		groupChecklistsByDate,
		osmTagToEmoji,
		groupLodgingByDate,
		LODGING_TYPES_ICONS
	} from '$lib';
	import ChecklistCard from '$lib/components/ChecklistCard.svelte';
	import ChecklistModal from '$lib/components/ChecklistModal.svelte';
	import AdventureModal from '$lib/components/AdventureModal.svelte';
	import TransportationModal from '$lib/components/TransportationModal.svelte';
	import CardCarousel from '$lib/components/CardCarousel.svelte';
	import { goto } from '$app/navigation';
	import LodgingModal from '$lib/components/LodgingModal.svelte';
	import LodgingCard from '$lib/components/LodgingCard.svelte';
	import ImageGallery from '$lib/components/ImageGallery.svelte'; // 1. Import the gallery component

	export let data: PageData;
	
	// 2. Add state management for the gallery
	let isGalleryOpen = false;
	let galleryImages: Adventure['images'] = [];
	let galleryStartIndex = 0;

	function openGallery(event: CustomEvent<{ images: Adventure['images']; index: number }>) {
		galleryImages = event.detail.images;
		galleryStartIndex = event.detail.index;
		isGalleryOpen = true;
	}

	function closeGallery() {
		isGalleryOpen = false;
	}

	// The rest of your existing script block...
	// ...
</script>

{#if currentView == 'all'}
		{#if adventures.length > 0}
			<h1 class="text-center font-bold text-4xl mt-4 mb-2">{$t('adventures.linked_adventures')}</h1>

			<div class="flex flex-wrap gap-4 mr-4 justify-center content-center">
				{#each adventures as adventure}
                    <div on:openGallery={openGallery}>
						<AdventureCard
							user={data.user}
							on:edit={editAdventure}
							on:delete={deleteAdventure}
							{adventure}
							{collection}
						/>
					</div>
				{/each}
			</div>
		{/if}

        {/if}

    {#if collection.start_date && collection.end_date}
		{#if currentView == 'itinerary'}
            {#if currentItineraryView == 'date'}
				{#each Array(numberOfDays) as _, i}
                        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
									{#if dayAdventures.length > 0}
										{#each dayAdventures as adventure}
                                            <div on:openGallery={openGallery}>
												<AdventureCard
													user={data.user}
													on:edit={editAdventure}
													on:delete={deleteAdventure}
													{adventure}
													{collection}
												/>
											</div>
										{/each}
									{/if}
                                    </div>
                        {/each}
                {:else}
				<ul class="relative">
								{#each orderedItems as orderedItem, index}
									<li class="relative pl-20 mb-8">
										<div class="bg-base-200 p-6 rounded-lg shadow-lg">
                                            {#if orderedItem.type === 'adventure' && orderedItem.item && 'images' in orderedItem.item}
                                                <div on:openGallery={openGallery}>
													<AdventureCard
														user={data.user}
														on:edit={editAdventure}
														on:delete={deleteAdventure}
														adventure={orderedItem.item}
														{collection}
													/>
												</div>
											{:else if orderedItem.type === 'transportation' && orderedItem.item && 'origin_latitude' in orderedItem.item}
                                                {/if}
										</div>
									</li>
								{/each}
							</ul>
                            </div>
					</div>
			{/if}
		{/if}
	{/if}

{#if isGalleryOpen}
	<ImageGallery
		images={galleryImages}
		startIndex={galleryStartIndex}
		on:close={closeGallery}
	/>
{/if}
