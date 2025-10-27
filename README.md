# 关系图谱（Relationship Map）

一个面向文学与影视作品的人物关系可视化项目。开箱即用的图谱视图与列表视图、深浅色主题、关系文字优化、居中信息卡动画与图片导出。默认示例为《红楼梦》，同时支持扩展到其他小说、电视剧、电影等作品的数据集。

## 功能特性

- 图谱视图（GraphView）
  - 支持力导/层级布局（可在代码中切换，默认 `force`）。
  - 点击节点弹出居中信息卡，显示姓名、性别、简介与标签。
  - 关系线文字清晰可读（加描边、深浅色适配）。
  - 图谱背景随主题切换（浅色/深色）保持协调。
  - 导出图谱为图片（`png`）。
  - 视图切换使用 `v-show` 避免重复渲染，显示时自动居中与适配尺寸。

- 列表视图（ListView）
  - 以卡片形式展示人物，显示姓名、性别符号、简介与标签。
  - 点击卡片弹出居中信息卡。

- 居中信息卡（PersonDetail）
  - 弹入/退场动画（淡入 + 轻微缩放）。
  - 显示姓名旁的性别符号（♀/♂）。
  - 移除了头像，强调文本信息的清晰与一致性。

- 深浅色主题
  - 基于 Tailwind CSS v4 的自定义 `dark` 变体，使用 HTML 根元素的 `.dark` 类切换主题。
  - 所有核心视图与关系线文字、图谱背景均进行深浅色适配。

- 顶部导航（右侧操作）
  - 视图切换（图谱/卡片）、导出图片、深浅色切换按钮统一在右侧。

## 技术栈

- Vue 3 + Vite
- Tailwind CSS v4（自定义 `dark` 变体）
- relation-graph-vue3（关系图谱可视化）
- lucide-vue-next（图标）

## 项目结构

```
relationship-map/
├── index.html
├── src/
│   ├── App.vue
│   ├── components/
│   │   ├── GraphView.vue
│   │   ├── ListView.vue
│   │   └── PersonDetail.vue
│   ├── data/
│   │   └── graph.json
│   ├── main.js
│   └── style.css
├── vite.config.js
├── package.json
└── README.md
```

## 快速开始

1. 安装依赖
   ```bash
   npm install
   ```

2. 启动开发环境
   ```bash
   npm run dev
   ```

3. 构建生产包
   ```bash
   npm run build
   ```

## 数据结构与扩展

项目数据位于 `src/data/graph.json`，由三部分组成：

- `rootId`: 默认聚焦的根节点 ID（可选）。
- `nodes`: 人物节点数组，每个节点包含基本样式与人物信息。
- `lines`: 关系边数组，描述节点之间的关系与关系文字。

节点示例：

```json
{
  "id": "jia_baoyu",
  "text": "贾宝玉",
  "color": "#4ea2f0",
  "borderColor": "#6cc0ff",
  "data": {
    "person": {
      "id": "jia_baoyu",
      "name": "贾宝玉",
      "gender": "male",
      "bio": "贾府后辈，荣国府的公子。",
      "tags": ["红楼梦", "贾府", "后辈"]
    }
  }
}
```

可选字段：可在数据集中加入 `phone`、`email` 等，用于在详情卡中展示。

关系示例：

```json
{ "from": "jia_baoyu", "to": "lin_daiyu", "text": "爱恋" }
```

### 如何扩展到其他作品（小说/电视剧/电影）

- 将 `graph.json` 中的 `nodes` 与 `lines` 替换或新增为目标作品的人物与关系即可。
- 建议：
  - 保持 `data.person` 字段结构一致（至少包含 `id`、`name`、`gender`、`bio`、`tags`）。
  - 针对不同阵营或家族，可使用不同的 `color` 与 `borderColor` 做区分。
  - 关系 `text` 保持简洁，例如 “朋友”、“亲属”、“同事”、“师徒”、“对手”等。
  - 如需更细粒度的关系类型和样式（线形、箭头、颜色），可在组件中统一映射配置。

## 主题与样式说明

- Tailwind CSS v4 自定义暗色变体在 `src/style.css` 中定义：
  ```css
  @custom-variant dark (&:where(.dark, .dark *));
  ```
- 关系图背景通过 CSS 变量 `--rg-bg` 控制，浅色与深色下分别覆盖 `relation-graph` 组件容器的背景。
- 关系文字（连线文本）增加描边与适配色，确保在深浅色模式下清晰可读。

## 交互与行为

- 视图切换：顶部右侧按钮在图谱与列表视图间切换。
- 节点/卡片点击：打开居中信息卡，展示人物基本信息与标签。
- 关闭弹窗：使用图标按钮关闭。
- 导出图片：图谱视图支持导出为 `png` 图片。

## 开发细节（实现思路）

- `GraphView.vue`：
  - 使用 `relation-graph-vue3` 加载 `graph.json` 并渲染。
  - 节点点击时向上抛出 `data.person` 对象。
  - 使用 `v-show` 保持组件挂载，避免重复初始化；显示时自动 `zoomToFit` 与 `moveToCenter` 适配。

- `ListView.vue`：
  - 从 `graph.json.nodes` 中提取 `data.person` 生成列表。
  - 卡片标题右侧显示性别符号（♀/♂）。

- `PersonDetail.vue`：
  - 居中弹窗，淡入 + 轻微缩放动画（`Transition name="pop"`）。
  - 姓名旁显示性别符号；移除头像展示。

- `App.vue`：
  - 顶部右侧统一放置视图切换、图片导出、深浅色切换按钮。
  - 切换深浅色：给 `document.documentElement` 添加或移除 `.dark` 类。

## 可选增强方向

- 增加搜索与筛选（按家族、标签、关系类型）。
- 关系类型统一配置（线形、箭头、颜色）与图例说明。
- 多数据源管理（支持作品切换与数据动态加载）。
- i18n 国际化与多语言支持。
- 移动端体验优化（更精细的触控区域与动画）。

## 致谢

- [relation-graph-vue3](https://www.npmjs.com/package/relation-graph-vue3)
- [Tailwind CSS](https://tailwindcss.com/)
- [Vue 3](https://vuejs.org/)、[Vite](https://vitejs.dev/)
- [lucide-vue-next](https://www.npmjs.com/package/lucide-vue-next)

## 许可证

本项目暂未指定开源许可证。如需开源或二次分发，请根据实际需求选择并添加相应的 License 文件（例如 MIT、Apache-2.0 等）。