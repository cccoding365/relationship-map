<script setup>
import { ref, computed, onMounted } from 'vue'
import { Share2, LayoutGrid, Sun, Moon, Download } from 'lucide-vue-next'
import GraphView from '@/components/GraphView.vue'
import ListView from '@/components/ListView.vue'
import PersonDetail from '@/components/PersonDetail.vue'
import people from '@/data/people.json'

const currentView = ref('graph') // 'graph' | 'list'
const isDark = ref(false)
const selectedPersonId = ref(null)
const graphViewRef = ref(null)

const selectedPerson = computed(() => {
  return people.find(p => p.id === selectedPersonId.value) || null
})

const toggleDark = () => {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
}

const onNodeSelect = (personId) => {
  selectedPersonId.value = personId
}

const closeDetail = () => {
  selectedPersonId.value = null
}

onMounted(() => {
  // 默认跟随系统暗色模式
  const prefersDark = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
  isDark.value = prefersDark
  document.documentElement.classList.toggle('dark', isDark.value)
})

const exportGraphImage = () => {
  graphViewRef.value?.exportImage?.()
}
</script>

<template>
  <div class="flex flex-col min-h-screen">
    <!-- 顶部导航栏 -->
    <header class="fixed top-0 inset-x-0 z-50 flex items-center gap-3 h-12 px-4 border-b border-gray-200 dark:border-gray-800 bg-white/70 dark:bg-gray-900/70 backdrop-blur supports-[backdrop-filter]:bg-white/50 dark:supports-[backdrop-filter]:bg-gray-900/50">
      <div class="text-lg font-bold">关系图谱</div>
      <nav class="flex items-center gap-2">
        <button
          :title="'图谱视图'"
          :class="['p-2 rounded-md transition-colors', currentView==='graph' ? 'bg-blue-600 text-white hover:bg-blue-700' : 'bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-200 hover:bg-gray-200 dark:hover:bg-gray-700']"
          @click="currentView='graph'"
        >
          <Share2 class="w-5 h-5" />
        </button>
        <button
          :title="'卡片视图'"
          :class="['p-2 rounded-md transition-colors', currentView==='list' ? 'bg-blue-600 text-white hover:bg-blue-700' : 'bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-200 hover:bg-gray-200 dark:hover:bg-gray-700']"
          @click="currentView='list'"
        >
          <LayoutGrid class="w-5 h-5" />
        </button>
      </nav>
      <div class="flex-1" />
      <div class="flex items-center gap-2">
        <button
          v-if="currentView==='graph'"
          :title="'导出图片'"
          class="p-2 rounded-md transition-colors bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-200 hover:bg-gray-200 dark:hover:bg-gray-700"
          @click="exportGraphImage"
        >
          <Download class="w-5 h-5" />
        </button>
        <button
          :title="isDark ? '切换为浅色' : '切换为深色'"
          class="p-2 rounded-md transition-colors bg-gray-100 dark:bg-gray-800 text-gray-700 dark:text-gray-200 hover:bg-gray-200 dark:hover:bg-gray-700"
          @click="toggleDark"
        >
          <component :is="isDark ? Sun : Moon" class="w-5 h-5" />
        </button>
      </div>
    </header>

    <!-- 内容区 -->
    <main class="flex-1 pt-12">
      <GraphView
        ref="graphViewRef"
        :visible="currentView==='graph'"
        @node-select="onNodeSelect"
        v-show="currentView==='graph'"
      />
      <ListView
        @person-select="onNodeSelect"
        v-show="currentView==='list'"
      />
    </main>

    <!-- 详情抽屉 -->
    <PersonDetail :person="selectedPerson" :visible="!!selectedPerson" @close="closeDetail" />
  </div>
</template>

<style>
/* 移动端适配：让 header 固定高度，内容区自适应 */
html, body, #app { height: 100%; }
</style>
