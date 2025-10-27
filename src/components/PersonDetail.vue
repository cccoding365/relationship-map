<script setup>
import { computed } from 'vue'

const props = defineProps({
  person: { type: Object, default: null },
  visible: { type: Boolean, default: false }
})
const emits = defineEmits(['close'])

const show = computed(() => props.visible && props.person)
</script>

<template>
  <div v-if="show" class="fixed inset-0 z-40">
    <div class="absolute inset-0 bg-black/30" @click="emits('close')" />
    <div class="absolute right-0 top-0 h-full w-full sm:w-[380px] bg-white dark:bg-gray-900 shadow-xl p-4 overflow-y-auto">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold">人物详情</h3>
        <button @click="emits('close')" class="px-2 py-1 rounded bg-gray-100 dark:bg-gray-800">关闭</button>
      </div>
      <div v-if="person" class="space-y-4">
        <div class="flex items-center gap-3">
          <img :src="person.avatar" alt="avatar" class="w-14 h-14 rounded-full object-cover" />
          <div>
            <div class="text-xl font-bold">{{ person.name }}</div>
            <div class="text-sm text-gray-500 dark:text-gray-400">{{ person.email }}</div>
          </div>
        </div>
        <p class="text-sm leading-relaxed">{{ person.bio }}</p>
        <div>
          <div class="text-sm font-medium mb-2">标签</div>
          <div class="flex flex-wrap gap-2">
            <span v-for="tag in person.tags" :key="tag" class="px-2 py-1 text-xs rounded bg-gray-100 dark:bg-gray-800">
              {{ tag }}
            </span>
          </div>
        </div>
        <div class="text-sm">
          <div class="font-medium">联系方式</div>
          <div class="mt-1">{{ person.phone }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>