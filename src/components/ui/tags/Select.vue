<script setup lang="ts">
import { useStore } from '@nanostores/vue';
import { tags } from '@/store.js';
import config from "@util/themeConfig";
import type Tag from "@/types/Tag";

const availableTags = config.directoryData.tags as Tag[] | undefined;

const selectedTags = useStore(tags);

function removeTag(tag: string) {
  const updatedTags = selectedTags.value.filter((t: string) => t !== tag);
  tags.set(updatedTags);
}

function addTagWithEvent(event: Event) {
  const select = event.target as HTMLSelectElement;
  const selectedTag = select.value;
  
  if (!selectedTag) return;
  
  if (!selectedTags.value.includes(selectedTag as never)) {
    tags.set([...selectedTags.value, selectedTag] as never[]);
  }
  
  select.value = "";
}
</script>

<template>
  <div class="flex flex-wrap gap-3">
    <div 
      v-for="myTag in selectedTags"
      :key="myTag"
      class="relative group inline-flex items-center gap-2 px-3 py-2 bg-primary-50 text-primary-700 border border-primary-200 rounded-full text-sm font-medium transition-all duration-200"
    >
      <span>{{ myTag }}</span>
      <button
        @click="() => removeTag(myTag)"
        class="ml-1 inline-flex items-center justify-center w-4 h-4 text-primary-500 hover:text-primary-700 hover:bg-primary-100 rounded-full transition-colors duration-200 focus:outline-none"
      >
        <slot />
      </button>
    </div>
    
    <select 
      @change="addTagWithEvent"
      class="inline-flex items-center px-4 py-2 border border-dashed border-gray-300 rounded-full text-sm font-medium text-gray-600 bg-white hover:bg-gray-50 hover:border-gray-400 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-primary-500 transition-all duration-200 cursor-pointer"
    >
      <option value="" disabled selected>
        + Add filter
      </option>
      <option v-for="tag in availableTags" :key="tag.key" :value="tag.key">
        {{ tag.emoji ? `${tag.emoji} ` : '' }}{{ tag.name }}
      </option>
    </select>
  </div>
</template>

<style scoped>
select {
  background-image: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 20 20'%3E%3Cpath stroke='%236b7280' stroke-linecap='round' stroke-linejoin='round' stroke-width='1.5' d='m6 8 4 4 4-4'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-size: 1.25em 1.25em;
  background-position: right 0.75rem center;
  padding-right: 2.5rem;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}
</style>