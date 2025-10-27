<script setup>
import { computed } from 'vue'
import { X } from 'lucide-vue-next'

const props = defineProps({
  person: { type: Object, default: null },
  visible: { type: Boolean, default: false }
})
const emits = defineEmits(['close'])

const show = computed(() => props.visible && props.person)
</script>

<template>
  <div v-if="show" class="fixed inset-0 z-40 flex items-center justify-center">
    <div class="absolute inset-0 bg-black/30" @click="emits('close')" />
    <Transition name="pop">
      <div class="relative w-[min(640px,calc(100%-2rem))] max-h-[80vh] overflow-y-auto rounded-lg shadow-xl bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-800 p-5">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold">人物信息</h3>
        <button @click="emits('close')" class="p-2 rounded bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700" aria-label="关闭">
          <X class="w-4 h-4" />
        </button>
      </div>
        <div v-if="person" class="space-y-4">
          <div class="flex items-center gap-2">
            <div class="text-xl font-bold">{{ person.name }}</div>
            <span v-if="person.gender === 'female'" class="text-pink-500" title="女性">♀</span>
            <span v-else-if="person.gender === 'male'" class="text-blue-500" title="男性">♂</span>
          </div>
          <p class="text-sm leading-relaxed text-gray-700 dark:text-gray-300">{{ person.bio || '暂无简介' }}</p>
          <div v-if="person.tags && person.tags.length">
            <div class="text-sm font-medium mb-2">标签</div>
            <div class="flex flex-wrap gap-2">
              <span v-for="tag in person.tags" :key="tag" class="px-2 py-1 text-xs rounded bg-gray-100 dark:bg-gray-800">
                {{ tag }}
              </span>
            </div>
          </div>
          <div v-if="person.phone || person.email" class="text-sm">
            <div class="font-medium">联系方式</div>
            <div class="mt-1 space-y-1">
              <div v-if="person.phone">{{ person.phone }}</div>
              <div v-if="person.email" class="text-gray-500 dark:text-gray-400">{{ person.email }}</div>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.pop-enter-active, .pop-leave-active {
  transition: transform 180ms ease, opacity 180ms ease;
}
.pop-enter-from, .pop-leave-to {
  opacity: 0;
  transform: translateY(6px) scale(0.98);
}
</style>