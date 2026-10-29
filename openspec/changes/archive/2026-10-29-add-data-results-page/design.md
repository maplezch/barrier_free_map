# Design

## Context

站点是无构建步骤的静态多页 HTML：`index.html` 与 `research-plan.html` 共用 `assets/css/styles.css`，无模板引擎、无 JavaScript、无后端。视觉语言由一套 CSS 变量与少量布局类组成（`research-layout`、`research-section`、`figure` 尚无既有样式）。目标宿主是 GitHub Pages，路径区分大小写。

约束：
- 运行环境没有 Pillow / ImageMagick / ffmpeg，但 Windows PowerShell 的 `System.Drawing` 可用（已实测读取 `图8.png`）。
- 原始资料 `data_collected.md` 与 8 张图保留不动，作为来源；页面不得成为新的唯一数据源。
- 现有 spec 明确禁止结果页，本次变更同时修改该约束（见 proposal.md）。

## Goals / Non-Goals

**Goals:**
- 新增结果页，且不引入构建工具、框架或运行时依赖。
- 图片既可在页面上清晰阅读，又不让单页加载过重。
- 三页导航与首页入口保持一致、无自相矛盾的文案。

**Non-Goals:**
- 不做图片灯箱、轮播或缩放交互（只用原图链接）。
- 不把 `data_collected.md` 渲染成 Markdown 源，正文手写为语义化 HTML。
- 不改动 `data_collected/` 内的原始文件，不删除它们。
- 不新增统计图表；图表沿用收集阶段已有的 8 张图。

## Decisions

### 1. 结果页复用 `research-plan.html` 的页面骨架
使用相同的 `skip-link` + `site-header` + `page-intro section-ink` + `research-layout`（左侧粘性目录 + 右侧内容栏）+ `site-footer` 结构，仅替换内容与编号（`项目总览 / 03`）。页内目录按两级：`00 结果概览`、`01 问卷数据分析`、`02 亲和图分析`。

替代方案：改用共享 HTML 片段或 JS 注入布局——会引入构建/运行时依赖，与无构建站点的现状不符。

### 2. 图片入库策略：复制到 `assets/images/`，ASCII 文件名
- 复制 8 张图到 `assets/images/`，命名 `results-fig-01.jpg` … `results-fig-06.jpg`、`results-fig-07.png`、`results-fig-08.png`。
- 需要缩略版的命名为 `results-fig-07-thumb.png`、`results-fig-08-thumb.png`。
- 原图同时保留在 `assets/images/` 中，作为 `target="_blank" rel="noopener"` 的链接目标。

理由：中文文件名在 URL 中会被百分号编码，链接难以阅读也容易在复制/部署时出错；`data_collected/` 继续充当原始归档，不承担站点资源目录的职责。代价是约 3.2MB 重复占用，可接受。

### 3. 缩略图只给图 7 和图 8，用 PowerShell `System.Drawing` 生成
- `results-fig-01.jpg` … `-06.jpg` 原图仅 416–567px 宽，本身就适合网页显示，直接使用，不再生成缩略图。
- `results-fig-07-thumb.png`：宽 1000px（高约 2015px），页面按内容栏宽度显示，文字仍可辨认。
- `results-fig-08-thumb.png`：宽 1600px（高约 536px），在内容栏中缩小显示。
- 生成脚本以一次性命令执行，不提交到仓库。

理由：重复压缩小图只会损失清晰度且无收益。替代方案（把原图直接铺进页面）会让首屏加载 3.2MB 且超宽图上的文字不可读。

### 4. 图表统一用 `<figure>`，图注取自图内标题，长图附"查看原图"
每张图的结构：

```html
<figure class="result-figure">
  <a class="result-figure-link" href="assets/images/results-fig-08.png" target="_blank" rel="noopener">
    <img src="assets/images/results-fig-08-thumb.png" alt="……">
  </a>
  <figcaption>图8 数字版亲和图…… <span>点击查看原尺寸图片</span></figcaption>
</figure>
```

图注按图内印制的标题照录；唯一例外是图8，其印字"5 个顶层主题、15 个子主题"与修正后的层级口径冲突，页面图注改用"5 个高级主题与 15 个主题"，并保留原图不改。

### 5. 主题层级口径统一为"15 个主题、5 个高级主题"
正文表述：33 张便利贴归并为 15 个主题，其中 14 个归入 5 个高级主题；另有 1 个主题「轻度、暂时性损伤者的需求不被识别」不隶属于任何高级主题。原文"5 个顶层主题和 15 个子主题"不再沿用。

### 6. 导航改为三项，当前页用 `is-active` + `aria-current`
`index.html`、`research-plan.html`、`data-results.html` 三页顶栏一致：首页 / 数据收集计划 / 数据收集结果。窄屏下导航沿用现有 flex 换行行为，不新增折叠菜单。

### 7. 首页与计划页的入口改动
- `index.html` 顶栏加入第三项；英雄区在「查看数据收集计划」旁增加「查看数据收集结果」文字链接。
- 时间线第 04 项「分析数据」的 `timeline-content` 内加入指向结果页的文字链接。
- 底部 CTA 面板由单按钮改为两个入口（计划 / 结果）。
- `research-plan.html` 底部提示语由"不展示后续收集结果"改为"结果已整理在数据收集结果页面"，并链接过去。

### 8. 样式追加到现有 `styles.css`
新增 `.result-figure`、`.result-figure img`、`.result-figure figcaption`、`.result-figure-link`、`.figure-note`，以及 CTA 双按钮的换行处理；继续复用现有配色变量与 `.research-section` 间距。不引入新字体或外部资源。

## Risks / Trade-offs

- [重复占用约 3.2MB] → 只在小图之外保留缩略图；`data_collected/` 可后续再清理，但本次不删。
- [图8 缩到 1600px 后便利贴文字偏小] → 明确提示"点击查看原尺寸图片"，原图链接始终可用。
- [移动端导航三项换行挤压] → 依赖既有 `flex-wrap` 行为，实施后在 375px 宽度下检查。
- [图注与图内印字不一致（图8）] → 图注用修正表述，正文说明口径；不改动图片本身。
- [正文手写 HTML 可能与 `data_collected.md` 漂移] → 保留 md 作为来源，图注与数字以 md 为准；本次不引入自动同步。

## Migration Plan

纯静态文件替换：新增页面与图片，修改两个既有页面和样式表。回滚即还原这三个文件并删除新增资源；无数据迁移、无缓存或部署配置变更。
