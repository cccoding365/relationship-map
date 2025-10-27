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
  const personId = nodeObject?.data?.personId || nodeObject?.id;
  emits("node-select", personId);
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

// 监听外部暗模式切换（如果有），刷新图谱以适配样式
watch(layoutName, () => {});

// 当组件重新可见时，进行一次居中/适配，避免隐藏期间尺寸为 0 造成的错位
watch(
  () => props.visible,
  async (v) => {
    if (v) {
      await nextTick();
      const instance = graphRef.value?.getInstance();
      if (!instance) return;
      try {
        await instance.zoomToFit();
      } catch (e) {}
      try {
        await instance.moveToCenter();
      } catch (e) {}
    }
  }
);

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
