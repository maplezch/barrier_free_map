# Proposal

## Why

站点目前只展示"数据收集计划"（准备做什么），但访谈和问卷已经完成，整理后的分析与结论（`data_collected/data_collected.md` 及 8 张图表）还没有任何公开入口。缺少结果页，项目进展时间线里的"分析数据"阶段也无处可去，读者无法从计划走到结论。

## What Changes

- 新增独立页面 `data-results.html`「数据收集结果」，编号"项目总览 / 03"，按 `data_collected.md` 的原有结构分节呈现问卷数据分析与亲和图分析，并补充页面导语、图注和图片替代文本。
- 将 `data_collected/` 下的 8 张图片整理进 `assets/images/`，使用英文文件名，并为每张图生成 web 用缩略图；原图保留，供读者点击查看。
- 首页增加通往结果页的入口：顶栏导航、英雄区、进度时间线第 04 项、底部 CTA 面板。
- 修正 `research-plan.html` 底部"不展示后续收集结果"的表述，改为指向结果页的说明，避免站内自相矛盾。
- **BREAKING**（相对既有 spec 约束）：`project-research-website` 原本明确禁止"数据收集结果页面"，本次需求方向相反，需要修改该约束；在线提交、存储等边界保持不变。

## Capabilities

### New Capabilities

（无）

### Modified Capabilities

- `project-research-website`:
  - 新增"展示数据收集结果"需求：提供独立结果页，按原文结构呈现问卷与亲和图的分析、图表和结论。
  - 修改"保持静态内容边界"：不再禁止结果页，仅保留"不提供在线提交、数据库存储和动态统计"的边界。
  - 修改"支持多页导航"：由两页导航扩展为三页，并保持当前页状态。
  - 修改"首页展示项目总览"：在既有入口之外，补充数据收集结果的入口。

## Impact

- 页面：新增 `data-results.html`；修改 `index.html`、`research-plan.html`。
- 样式：复用 `assets/css/styles.css`，按需追加图表与长图展示样式。
- 资源：新增 `assets/images/` 下的 8 张图片及其缩略图；`data_collected/` 保留为原始资料，不删除。
- 部署：仍是纯静态站点，GitHub Pages 无需配置变更。
- 不涉及：访谈/问卷提交功能、后端服务、数据库、动态统计图表。
