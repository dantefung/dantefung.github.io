# 项目实现思路与开发记录（2025-11-11）

本文记录本仓库近期为自动化部署、构建性能与站点字体优化所做的实现思路与修改清单，便于后续维护与迭代。

## 目标
- 自动构建并发布 Hexo 静态站点至 `master` 分支。
- 在 CI 中使用较新的 Node 版本与更快的包管理器，并启用缓存。
- 清理无用资源，减小仓库体积。
- 为站点启用「霞鹜文楷（LXGW WenKai）」字体以改善中文阅读体验。
- 产出可操作的文档与故障排查说明。

## 实现思路总览
- GitHub Actions 工作流
  - 新增 `.github/workflows/hexo-deploy.yml`，限定只在源码分支 `blog-src` 推送时触发；构建后用 `peaceiris/actions-gh-pages` 将 `public/` 发布到 `master`。
  - 使用 Node 22 + pnpm，配置 `cache: pnpm` 与 `cache-dependency-path: pnpm-lock.yaml`，加速依赖安装；修正步骤顺序为「先安装 pnpm → 再 setup-node 缓存」。
  - 通过 `permissions: contents: write` 与仓库级 “Read and write permissions” 使用内置 `GITHUB_TOKEN` 推送，无需额外 PAT。
- 文档与可视化
  - 新增 `docs/hexo-actions-deploy.md`：包含必需/可选配置、GITHUB_TOKEN 说明与 Mermaid 流程图（采用兼容语法避免解析错误）。
- 资源清理
  - 评估并移除未引用的根目录 `assets/`（含 `assets/js/DPlayer.min.js`），减少冗余与潜在混淆。
- 字体支持
  - 在 `source/css/lxgw-wenkai.css` 以 @import 引入 WebFont；在 `/_config.fluid.yml` 配置 `font.font_family` 与 `custom_css`，使主题读取并应用字体。

## 变更清单（按文件）
- `.github/workflows/hexo-deploy.yml`
  - 触发分支：`on.push.branches: [blog-src]`
  - Node：`22`
  - 包管理器：`pnpm/action-setup@v4`（先安装）
  - 缓存：`actions/setup-node@v4` with `cache: pnpm` + `cache-dependency-path: pnpm-lock.yaml`
  - 构建：`pnpm exec hexo clean && pnpm exec hexo generate`
  - 发布：`peaceiris/actions-gh-pages@v3` 推送到 `master`，提交信息包含 `[skip ci]`
- `docs/hexo-actions-deploy.md`
  - 使用指南、必需/可选配置、GITHUB_TOKEN 结论与注意点、故障排查
  - Mermaid 流程图（简化节点文本，避免中括号/模板表达式引发解析错误）
- `README.md`
  - 增补 Actions 使用说明的入口与简要说明（指向 docs）
- `assets/`（已清理）
  - 删除：`assets/js/DPlayer.min.js` 与空目录
- `/_config.fluid.yml`
  - `font.font_family: 'LXGW WenKai','LXGW WenKai Screen', var(--font-family-sans-serif)`
  - `custom_css: [/css/lxgw-wenkai.css]`
- `source/css/lxgw-wenkai.css`
  - 通过 jsDelivr 引入 `lxgw-wenkai-webfont`，并保留扩展点以便后续细化样式

## 决策与取舍
- CI 包管理器选型：pnpm 较 npm 有更佳的安装性能与缓存命中；结合 `cache: pnpm` 能减少冷启动耗时。
- 字体加载策略：优先 WebFont（CDN）方案，改动小且可快速验证；如需离线/内网环境，可切换为自托管 woff2（后续可选）。
- 工作流触发与避免循环：仅在 `blog-src` 触发，且发布提交附加 `[skip ci]`，避免 `master` 的发布变更再次触发。
- Mermaid 渲染兼容性：去除方括号、模板表达式等容易被 Markdown/渲染器误解析的符号，保证在 GitHub 与常见渲染器下稳定显示。

## 常见问题与对应修复
- “Unable to locate executable file: pnpm”
  - 原因：`actions/setup-node` 在启用 `cache: pnpm` 时可能会在 pnpm 安装前尝试调用 pnpm。
  - 修复：将 `pnpm/action-setup` 步骤前置，之后再调用 `actions/setup-node` 开启 pnpm 缓存。
- Mermaid 报错 “got 'LINK_ID'”
  - 原因：节点文本中包含 `[]`、反引号、`${{ }}` 等特殊字符触发语法冲突。
  - 修复：改用圆角节点与简单文案，必要处用 `<br/>` 或简短 label。
- Pages 无法发布或 404
  - 检查仓库 Settings → Pages 是否选择 `master / (root)`；确认 Actions 权限为 “Read and write permissions”。

## 开发记录（时间线）
- 初始化工作流：新增 `hexo-deploy.yml`，默认忽略 `master` 触发；后续根据约定改为仅 `blog-src` 触发。
- 增补 README 与 `docs/hexo-actions-deploy.md`，追加 GITHUB_TOKEN 结论与 Mermaid 图。
- 将 Node 升级到 22，切换 pnpm + 缓存；修复 pnpm 执行顺序导致的找不到可执行文件问题。
- 评估并删除未引用的 `assets/js/DPlayer.min.js`，清理空目录。
- 引入「霞鹜文楷」字体：添加 `source/css/lxgw-wenkai.css` 与主题配置，完成全站生效。

## 后续建议
- 如需摆脱外链依赖：改为自托管 2~3 款常用字重的 woff2，并在 CSS 中配置 `font-display: swap`。
- 若主题为子模块：给 `actions/checkout` 添加 `submodules: recursive`。
- 缓存强化：可叠加 `actions/cache` 缓存 pnpm store（在 Linux runner 下路径通常为 `~/.pnpm-store`）。

