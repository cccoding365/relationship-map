<script setup>
import { ref, onMounted, watch, nextTick } from "vue";
import RelationGraph from "relation-graph-vue3";
import graphJson from "@/data/graph.json";

const emits = defineEmits(["node-select"]);
const props = defineProps({
  visible: { type: Boolean, default: true },
});

const graphRef = ref();
const layoutName = ref("force");
const graphOptions = ref({
  defaultNodeBorderWidth: 0,
  defaultNodeColor: "#4ea2f0",
  defaultLineShape: 1,
  defaultJunctionPoint: "border",
  layout: { layoutName: "force" },
  allowSwitchLineShape: true,
  allowSwitchJunctionPoint: true,
  zoomToFitWhenRefresh: true,
  moveToCenterWhenRefresh: true,
  defaultFocusRootNode: false,
});

const loadGraph = async () => {
  const instance = graphRef.value?.getInstance();
  if (!instance) return;
  await graphRef.value.setJsonData(graphJson, () => {
    // 图谱加载完成回调
  });
  await instance.zoomToFit();
};

const onNodeClick = (nodeObject) => {
  const person = nodeObject?.data?.person;
  if (person) {
    emits("node-select", person);
  } else {
    emits("node-select", { id: nodeObject?.id, name: nodeObject?.text });
  }
  return true;
};

const setLayout = async (name) => {
  layoutName.value = name;
  const instance = graphRef.value?.getInstance();
  if (!instance) return;
  instance.setOptions({ layout: { layoutName: name } });
  await instance.doLayout();
  await instance.moveToCenter();
};

const exportImage = async () => {
  const instance = graphRef.value?.getInstance();
  if (!instance) return;
  if (instance.downloadAsImage) {
    instance.downloadAsImage("png", "relationship-map.png");
  } else if (instance.getImageBase64) {
    const base64 = await instance.getImageBase64();
    const a = document.createElement("a");
    a.href = base64;
    a.download = "relationship-map.png";
    a.click();
  }
};

onMounted(() => {
  loadGraph();
});

// 暴露方法供父组件调用（如顶部导航触发导出）
defineExpose({ exportImage });
</script>

<template>
  <div class="w-full h-[calc(100vh-3rem)]">
    <RelationGraph
      ref="graphRef"
      :options="graphOptions"
      :on-node-click="onNodeClick"
    />
  </div>
</template>

<style scoped></style>
