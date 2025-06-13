<script setup lang="ts">
import { useStore } from '@nanostores/vue';
import { tags } from '@/store.js';
import config from "@util/themeConfig";
import type Tag from "@/types/Tag";

const availableTags = config.directoryData.tags as Tag[] | undefined;

const selectedTags = useStore(tags);

function toggleTagByName(tag: string) {
  if (!tag) return;
  
  if (!selectedTags.value.includes(tag as never)) {
    tags.set([...selectedTags.value, tag] as never[]);
  }
  else {
    let filtered = selectedTags.value.filter(e => e !== tag);
    tags.set([...filtered]);
  }
}
</script>

<template>
  <div class="flex flex-wrap gap-3">
    <button
      v-for="tag in availableTags"
      :key="tag.key"
      class="inline-flex items-center gap-2 px-4 py-2 rounded-full text-sm font-medium border transition-all duration-200 hover:shadow-sm focus:outline-none focus:ring-2 focus:ring-primary-500 focus:ring-offset-2"
      :class="selectedTags.includes(tag.key) 
        ? 'bg-primary-50 text-primary-700 border-primary-200 hover:bg-primary-100' 
        : 'bg-white text-gray-700 border-gray-200 hover:bg-gray-50 hover:border-gray-300'"
      @click="toggleTagByName(tag.key)"
    >
      <span v-if="tag.emoji" class="text-base">{{ tag.emoji }}</span>
      <span>{{ tag.name }}</span>
    </button>
  </div>
</template>