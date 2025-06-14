<script setup lang="ts">
import { ref, computed } from 'vue';
import { useStore } from '@nanostores/vue';
import { Icon } from 'astro-icon/components';
import config from "@util/themeConfig";

// Store for selected tools
const selectedTools = ref<any[]>([]);
const showComparison = ref(false);

// Get all available tools (this would be passed as props in real implementation)
const props = defineProps<{
  allTools: any[]
}>();

const availableTools = computed(() => 
  props.allTools.filter(tool => !selectedTools.value.find(selected => selected.id === tool.id))
);

function addTool(tool: any) {
  if (selectedTools.value.length < 3) {
    selectedTools.value.push(tool);
  }
}

function removeTool(toolId: string) {
  selectedTools.value = selectedTools.value.filter(tool => tool.id !== toolId);
}

function clearAll() {
  selectedTools.value = [];
  showComparison.value = false;
}

function startComparison() {
  if (selectedTools.value.length >= 2) {
    showComparison.value = true;
  }
}

// Extract key features for comparison
function getToolFeatures(tool: any) {
  const features = {
    pricing: 'From $29', // This would be extracted from tool data
    processingTime: '1-2 hours',
    quality: '4K Resolution',
    styles: '50+ Styles',
    teamFeatures: tool.tags?.includes('team_staff') ? 'Yes' : 'No',
    freeOption: tool.tags?.includes('free') ? 'Yes' : 'No',
    mobileApp: 'No', // This would be in tool data
    apiAccess: 'No'
  };
  return features;
}

const comparisonCategories = [
  { key: 'pricing', label: 'Starting Price', icon: 'tabler:currency-dollar' },
  { key: 'processingTime', label: 'Processing Time', icon: 'tabler:clock' },
  { key: 'quality', label: 'Image Quality', icon: 'tabler:photo' },
  { key: 'styles', label: 'Style Options', icon: 'tabler:palette' },
  { key: 'teamFeatures', label: 'Team Features', icon: 'tabler:users' },
  { key: 'freeOption', label: 'Free Option', icon: 'tabler:gift' },
  { key: 'mobileApp', label: 'Mobile App', icon: 'tabler:device-mobile' },
  { key: 'apiAccess', label: 'API Access', icon: 'tabler:api' }
];
</script>

<template>
  <div class="bg-white rounded-2xl shadow-lg border border-gray-200 p-6 mb-8">
    <!-- Header -->
    <div class="flex items-center justify-between mb-6">
      <div>
        <h2 class="text-2xl font-bold text-gray-900 mb-2">Compare AI Headshot Tools</h2>
        <p class="text-gray-600">Select up to 3 tools to compare their features, pricing, and capabilities side by side.</p>
      </div>
      <div class="text-right">
        <div class="text-sm text-gray-500 mb-1">Selected Tools</div>
        <div class="text-2xl font-bold text-primary-600">{{ selectedTools.length }}/3</div>
      </div>
    </div>

    <!-- Tool Selection -->
    <div v-if="!showComparison" class="space-y-4">
      <!-- Selected Tools -->
      <div v-if="selectedTools.length > 0" class="mb-6">
        <h3 class="text-lg font-semibold text-gray-900 mb-3">Selected for Comparison</h3>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div 
            v-for="tool in selectedTools" 
            :key="tool.id"
            class="bg-primary-50 border border-primary-200 rounded-xl p-4 relative"
          >
            <button 
              @click="removeTool(tool.id)"
              class="absolute top-2 right-2 w-6 h-6 bg-red-100 hover:bg-red-200 text-red-600 rounded-full flex items-center justify-center transition-colors duration-200"
            >
              <Icon name="tabler:x" class="w-4 h-4" />
            </button>
            <div class="flex items-center gap-3 mb-2">
              <img 
                v-if="tool.logo" 
                :src="tool.logo" 
                :alt="`${tool.title} logo`"
                class="w-8 h-8 object-contain"
              />
              <div v-else class="w-8 h-8 bg-primary-200 rounded-lg flex items-center justify-center">
                <span class="text-primary-700 font-semibold text-sm">{{ tool.title.charAt(0) }}</span>
              </div>
              <h4 class="font-semibold text-gray-900">{{ tool.title }}</h4>
            </div>
            <p class="text-sm text-gray-600 line-clamp-2">{{ tool.description }}</p>
          </div>
        </div>
        
        <!-- Action Buttons -->
        <div class="flex gap-3 mt-4">
          <button 
            @click="startComparison"
            :disabled="selectedTools.length < 2"
            class="btn-primary disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <Icon name="tabler:compare" class="w-4 h-4 mr-2" />
            Compare Tools ({{ selectedTools.length }})
          </button>
          <button @click="clearAll" class="btn-secondary">
            <Icon name="tabler:trash" class="w-4 h-4 mr-2" />
            Clear All
          </button>
        </div>
      </div>

      <!-- Available Tools -->
      <div v-if="selectedTools.length < 3">
        <h3 class="text-lg font-semibold text-gray-900 mb-3">
          {{ selectedTools.length === 0 ? 'Choose Tools to Compare' : 'Add More Tools' }}
        </h3>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 max-h-96 overflow-y-auto">
          <button
            v-for="tool in availableTools"
            :key="tool.id"
            @click="addTool(tool)"
            class="text-left bg-gray-50 hover:bg-gray-100 border border-gray-200 hover:border-gray-300 rounded-xl p-4 transition-all duration-200 group"
          >
            <div class="flex items-center gap-3 mb-2">
              <img 
                v-if="tool.logo" 
                :src="tool.logo" 
                :alt="`${tool.title} logo`"
                class="w-8 h-8 object-contain"
              />
              <div v-else class="w-8 h-8 bg-gray-200 rounded-lg flex items-center justify-center">
                <span class="text-gray-600 font-semibold text-sm">{{ tool.title.charAt(0) }}</span>
              </div>
              <h4 class="font-semibold text-gray-900 group-hover:text-primary-600 transition-colors duration-200">
                {{ tool.title }}
              </h4>
            </div>
            <p class="text-sm text-gray-600 line-clamp-2">{{ tool.description }}</p>
            <div class="flex flex-wrap gap-1 mt-2">
              <span 
                v-for="tag in tool.tags?.slice(0, 2)" 
                :key="tag"
                class="text-xs bg-gray-200 text-gray-700 px-2 py-1 rounded-full"
              >
                {{ config.directoryData.tags.find(t => t.key === tag)?.name || tag }}
              </span>
            </div>
          </button>
        </div>
      </div>
    </div>

    <!-- Comparison View -->
    <div v-if="showComparison" class="space-y-6">
      <!-- Header with tools -->
      <div class="flex items-center justify-between">
        <h3 class="text-xl font-bold text-gray-900">Tool Comparison</h3>
        <button @click="showComparison = false" class="btn-secondary">
          <Icon name="tabler:arrow-left" class="w-4 h-4 mr-2" />
          Back to Selection
        </button>
      </div>

      <!-- Comparison Table -->
      <div class="overflow-x-auto">
        <table class="w-full border-collapse">
          <!-- Tool Headers -->
          <thead>
            <tr class="border-b border-gray-200">
              <th class="text-left py-4 px-4 font-semibold text-gray-900 w-48">Features</th>
              <th 
                v-for="tool in selectedTools" 
                :key="tool.id"
                class="text-center py-4 px-4 min-w-48"
              >
                <div class="flex flex-col items-center gap-2">
                  <img 
                    v-if="tool.logo" 
                    :src="tool.logo" 
                    :alt="`${tool.title} logo`"
                    class="w-12 h-12 object-contain"
                  />
                  <div v-else class="w-12 h-12 bg-gray-200 rounded-xl flex items-center justify-center">
                    <span class="text-gray-600 font-semibold">{{ tool.title.charAt(0) }}</span>
                  </div>
                  <div>
                    <h4 class="font-semibold text-gray-900">{{ tool.title }}</h4>
                    <div class="flex flex-wrap gap-1 mt-1 justify-center">
                      <span 
                        v-for="tag in tool.tags?.slice(0, 2)" 
                        :key="tag"
                        class="text-xs bg-gray-100 text-gray-600 px-2 py-1 rounded-full"
                      >
                        {{ config.directoryData.tags.find(t => t.key === tag)?.name || tag }}
                      </span>
                    </div>
                  </div>
                </div>
              </th>
            </tr>
          </thead>

          <!-- Comparison Rows -->
          <tbody>
            <tr v-for="category in comparisonCategories" :key="category.key" class="border-b border-gray-100 hover:bg-gray-50">
              <td class="py-4 px-4">
                <div class="flex items-center gap-2">
                  <Icon :name="category.icon" class="w-5 h-5 text-gray-500" />
                  <span class="font-medium text-gray-900">{{ category.label }}</span>
                </div>
              </td>
              <td 
                v-for="tool in selectedTools" 
                :key="tool.id"
                class="py-4 px-4 text-center"
              >
                <span class="text-gray-700">{{ getToolFeatures(tool)[category.key] }}</span>
              </td>
            </tr>

            <!-- Description Row -->
            <tr class="border-b border-gray-100">
              <td class="py-4 px-4">
                <div class="flex items-center gap-2">
                  <Icon name="tabler:info-circle" class="w-5 h-5 text-gray-500" />
                  <span class="font-medium text-gray-900">Description</span>
                </div>
              </td>
              <td 
                v-for="tool in selectedTools" 
                :key="tool.id"
                class="py-4 px-4 text-center"
              >
                <p class="text-sm text-gray-600 line-clamp-3">{{ tool.description }}</p>
              </td>
            </tr>

            <!-- Action Row -->
            <tr>
              <td class="py-4 px-4">
                <div class="flex items-center gap-2">
                  <Icon name="tabler:external-link" class="w-5 h-5 text-gray-500" />
                  <span class="font-medium text-gray-900">Try Tool</span>
                </div>
              </td>
              <td 
                v-for="tool in selectedTools" 
                :key="tool.id"
                class="py-4 px-4 text-center"
              >
                <div class="space-y-2">
                  <a 
                    :href="tool.link" 
                    target="_blank"
                    rel="noopener noreferrer"
                    class="inline-flex items-center gap-1 bg-primary-600 hover:bg-primary-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors duration-200"
                  >
                    Visit Site
                    <Icon name="tabler:external-link" class="w-4 h-4" />
                  </a>
                  <br>
                  <a 
                    :href="`/${tool.id}`"
                    class="inline-flex items-center gap-1 text-primary-600 hover:text-primary-700 text-sm font-medium transition-colors duration-200"
                  >
                    Read Review
                    <Icon name="tabler:arrow-right" class="w-4 h-4" />
                  </a>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>