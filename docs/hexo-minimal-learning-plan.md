# Hexo 学习路线（最小知识原则）

目标：用最少必要知识，快速稳定地写作、预览与发布本项目的 Hexo 站点；其余进阶在需要时再学。

## 第0阶：能跑起来（10 分钟）
- 你需要会
  - 仓库结构：`source/` 写内容，`public/` 构建结果（无需手改），`themes/` 是主题（本项目：Fluid）。
  - 常用命令：
    - `pnpm exec hexo s` 本地预览
    - `pnpm exec hexo g` 生成静态文件
    - `pnpm exec hexo clean` 清缓存与输出
- 马上做
  - 新文章：`pnpm exec hexo new post "我的第一篇"`
  - 预览：`pnpm exec hexo s` → 浏览器打开提示地址
- 暂时忽略
  - 主题源码细节、插件内部实现

## 第1阶：写作必需（20 分钟）
- 你需要会
  - Front‑matter（文章头部元信息）最少字段：
    - `title`、`date`（自动）、`tags`、`categories`
  - 图片与附件（已开启 `post_asset_folder: true`）：
    - 文章 `foo.md` 对应资源放 `source/_posts/foo/`，Markdown 引用 `![alt](foo/xxx.png)`
- 马上做
  - 在 `source/_posts/` 写 2 篇文章，使用 tags、categories，并插入一张图片
- 暂时忽略
  - 自定义永久链接、摘要截断的高级玩法

## 第2阶：主题常用开关（15 分钟）
- 你需要会
  - 仅操作根部主题覆写文件：`_config.fluid.yml`
  - 本项目已改动：
    - 字体：`font.font_family` 使用“霞鹜文楷”
    - 自定义样式：`custom_css: [/css/lxgw-wenkai.css]`
    - 代码高亮：`code.highlight.lib: highlightjs`
- 马上做
  - 调整字号或全局颜色（`font.font_size`、`color.*`），刷新本地预览观察效果
- 暂时忽略
  - 主题内部 Stylus/Sass 源码、复杂布局改造

## 第3阶：本项目已用到的插件（20 分钟）
- 你需要会（只记用途与入口）
  - 渲染器：`hexo-renderer-marked`（Markdown）、`hexo-renderer-ejs`（主题模板）
  - 生成器：index/archives/category/tag（自动生成导航页）
  - 标签：`hexo-tag-dplayer`（插入视频）
  - 加密：`hexo-blog-encrypt`（可选，文章级加密）
- 马上做
  - 在一篇文章里使用 dplayer 标签（参考 `source/_posts/测试插入视频.md`）
- 暂时忽略
  - 渲染器切换、编写自定义标签/过滤器

## 第4阶：发布与 CI（15 分钟）
- 你需要会
  - 分支约定：源码在 `blog-src`，生成站点自动发布到 `master`
  - GitHub Pages：Setting → Pages 选择 `master / (root)`
  - 工作流要点：Node 22 + pnpm + 缓存；推送 `blog-src` 触发构建
- 马上做
  - `git add -A && git commit -m "post: 新文章" && git push`（到 `blog-src`）
  - Actions 页面查看“Build and Deploy Hexo to master”日志
- 暂时忽略
  - 手工 `hexo d`（CI 已自动发布）

## 第5阶：故障快速排查（10 分钟）
- 页面 404 或未更新：
  - Pages 是否指向 `master/(root)`；工作流是否成功；`public/` 是否生成
- 构建失败：
  - 看 Actions 日志：依赖安装（pnpm）、渲染报错（常见为 Markdown/标签拼写）
- 样式/字体没生效：
  - `_config.fluid.yml.custom_css` 是否包含 `/css/lxgw-wenkai.css`；浏览器缓存清理后再看
- dplayer 无法播放：
  - 媒体 URL 是否可公网访问、HTTPS 是否正确

## 进阶路线（按需再学）
- 路由与永久链接、摘要与 SEO、sitemap/feed
- 主题二次开发（自定义 partial、helper、注入点）
- 自托管字体与资源、构建优化（Prism、图片压缩、PWA）
- 自定义脚本（`scripts/`）与过滤器、生成器

## 写作清单（每日复用）
- 新建：`pnpm exec hexo new post "标题"`
- 写作：在 `_posts/标题/` 放图，引用 `![图](标题/xxx.png)`
- 本地看：`pnpm exec hexo s`
- 提交：`git add -A && git commit -m "post: 标题" && git push`
- 线上看：等 Actions 成功 → 打开 Pages

## 文件入口对照（仅记这几处）
- 站点配置：`_config.yml`
- 主题配置覆写：`_config.fluid.yml`
- 文章与资源：`source/_posts/`
- 自定义样式：`source/css/lxgw-wenkai.css`
- CI 工作流：`.github/workflows/hexo-deploy.yml`
