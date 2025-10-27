<script setup>
import { computed } from "vue";
import { X } from 'lucide-vue-next'

const props = defineProps({
  visible: { type: Boolean, default: false },
  current: { type: String, default: "" },
  options: { type: Array, default: () => [] },
});

const emit = defineEmits(["close", "select"]);

const items = computed(() => props.options || []);

const onSelect = (name) => {
  emit("select", name);
};

const onClose = () => emit("close");
</script>

<template>
  <div v-if="visible" class="fixed inset-0 z-50 flex items-center justify-center">
    <div class="absolute inset-0 bg-black/30" @click="onClose" />
    <div class="relative w-[min(520px,calc(100%-2rem))] rounded-lg shadow-xl bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-800 p-5">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold">选择数据源</h3>
        <button class="p-2 rounded bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700" @click="onClose">
          <X class="w-4 h-4" />
        </button>
      </div>
      <div class="space-y-3">
        <button
          v-for="name in items"
          :key="name"
          class="w-full text-left px-4 py-3 rounded-md border border-gray-200 dark:border-gray-800 hover:bg-gray-50 dark:hover:bg-gray-800"
          :class="current === name ? 'bg-blue-50 dark:bg-blue-900/20 border-blue-300 dark:border-blue-800' : ''"
          @click="onSelect(name)"
        >
          {{ `《${name}》` }}
        </button>
      </div>
    </div>
  </div>
</template>