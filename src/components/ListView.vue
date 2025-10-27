<script setup>
import people from '@/data/people.json'
const emits = defineEmits(['person-select'])
</script>

<template>
  <div class="p-4">
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
      <div
        v-for="p in people"
        :key="p.id"
        class="rounded border border-gray-200 dark:border-gray-800 bg-white dark:bg-gray-900 shadow-sm overflow-hidden"
      >
        <button class="w-full text-left" @click="emits('person-select', p.id)">
          <div class="p-4">
            <div class="font-semibold">{{ p.name }}</div>
            <div class="text-xs text-gray-500 dark:text-gray-400">{{ p.email }}</div>
          </div>
          <div class="px-4 pb-4">
            <p class="text-sm text-gray-700 dark:text-gray-300 line-clamp-3">{{ p.bio }}</p>
            <div class="mt-2 flex flex-wrap gap-2">
              <span
                v-for="tag in p.tags"
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