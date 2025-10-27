<script setup>
import { computed } from 'vue'
const emits = defineEmits(['person-select'])
const props = defineProps({ data: { type: Object, default: null } })
const people = computed(() => (props.data?.nodes || [])
  .map(n => n.data?.person)
  .filter(Boolean))
</script>

<template>
  <div class="p-4">
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
      <div
        v-for="p in people"
        :key="p.id"
        class="rounded border border-gray-200 dark:border-gray-800 bg-white dark:bg-gray-900 shadow-sm overflow-hidden"
      >
        <button class="w-full text-left" @click="emits('person-select', p)">
          <div class="p-4">
            <div class="font-semibold flex items-center gap-2">
              <span>{{ p.name }}</span>
              <span v-if="p.gender === 'female'" class="text-pink-500" title="女性">♀</span>
              <span v-else-if="p.gender === 'male'" class="text-blue-500" title="男性">♂</span>
            </div>
          </div>
          <div class="px-4 pb-4">
            <p class="text-sm text-gray-700 dark:text-gray-300 line-clamp-3">{{ p.bio || '—' }}</p>
            <div class="mt-2 flex flex-wrap gap-2">
              <span
                v-for="tag in (p.tags || [])"
                :key="tag"
                class="px-2 py-1 text-xs rounded bg-gray-100 dark:bg-gray-800"
              >{{ tag }}</span>
            </div>
          </div>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>