# Nebula ERP · 国产组件库与模板示例

本原型基于 React + Ant Design（CDN 方式）实现，聚焦国产 ERP 常见界面范式。打开 `erp-app.html` 即可体验桌面端优先的组件库、模板页、看板与系统设置示例，无需任何构建步骤。

## 使用方式

1. 在浏览器中打开 `erp-app.html`（推荐 1280px 以上分辨率）。
2. 顶部工具栏提供：
   - 全局搜索、通知中心、帮助入口；
   - 租户与角色切换（管理员/采购员/访客）；
   - 主题（浅色/深色）、界面密度（紧凑/默认）、语言（zh-CN/en-US）切换。
3. 左侧侧栏可在看板、组件库、模板页、系统设置之间切换；主工作区支持页签式多任务。

## 关键特性说明

- **组件库**：覆盖 50+ ERP 常用组件，支持搜索、分类树筛选与密度切换。卡片内可直接预览交互、查看配置、复制示例片段。
- **模板页**：展示“列表 → 详情 → 编辑 → 审批”全链路范式。
  - 列表页包含高级筛选抽屉、列设置持久化、批量操作、导入/导出/打印、视图保存等功能。
  - 详情页涵盖基础信息、明细子表、附件、关联单据、审计日志。
  - 编辑页实现行编辑明细表、跨字段联动计算、草稿自动保存与快捷键提示。
  - 审批页提供可视化流程、审批动作（同意/驳回/转办/加签）、意见与附件校验。
- **看板总览**：展示 KPI 卡片、待办/审批/预警、ECharts 可视化的采购趋势与供应商集中度。
- **系统设置**：示范数据字典、编号规则、打印模板管理与权限示例。
- **权限与多租户**：切换角色即可观察菜单与按钮级权限变化；租户切换保留独立状态。
- **i18n & 主题**：全局及组件库页均支持语言、主题、密度切换；文本采用 zh-CN / en-US 双语配置。

## CDN 访问受限时

- 页面会依次尝试从 `unpkg`、`jsDelivr` 以及本地 `./vendor/` 目录加载 React、Ant Design、dayjs、ECharts、Babel 等依赖；若全部失败会在页面内提示缺失项。
- 如处于离线或内网环境，可在 `assistant_outputs/inerface/vendor/` 目录手动存放以下文件（文件名需与列表一致）：
  - `react.development.js`、`react-dom.development.js`
  - `antd.min.js`、`ant-design-icons.umd.js`
  - `dayjs.min.js`、`dayjs-plugin-customParseFormat.js`
  - `echarts.min.js`、`babel.min.js`
- 获取示例：
  ```bash
  npm install react@18.2.0 react-dom@18.2.0 antd@5.14.2 @ant-design/icons@5.2.6 dayjs@1.11.11 echarts@5.5.0 @babel/standalone@7.23.9
  mkdir -p assistant_outputs/inerface/vendor
  cp node_modules/react/umd/react.development.js assistant_outputs/inerface/vendor/
  cp node_modules/react-dom/umd/react-dom.development.js assistant_outputs/inerface/vendor/
  cp node_modules/antd/dist/antd.min.js assistant_outputs/inerface/vendor/
  cp node_modules/@ant-design/icons/dist/index.umd.js assistant_outputs/inerface/vendor/ant-design-icons.umd.js
  cp node_modules/dayjs/dayjs.min.js assistant_outputs/inerface/vendor/
  cp node_modules/dayjs/plugin/customParseFormat.js assistant_outputs/inerface/vendor/dayjs-plugin-customParseFormat.js
  cp node_modules/echarts/dist/echarts.min.js assistant_outputs/inerface/vendor/
  cp node_modules/@babel/standalone/babel.min.js assistant_outputs/inerface/vendor/
  ```
- 重新启动或刷新本地静态服务器后即可离线访问原型。

## 定制与扩展建议

- 所有数据源均使用内存 Mock，可在脚本顶部替换为实际 API。
- 组件库卡片可结合 JSON Schema / 元数据驱动，实现字段、表格的配置化。
- 若需进一步拆分组件，可将内联脚本拆分为模块化 React 项目，引入状态管理与路由。

如需新增页面或交互，可在主脚本中扩展 `menuConfig` 和对应页面组件，并复用现有数据结构即可。

