# 本项目依赖与 Hexo 生态归档

本文按类别梳理本项目实际安装与使用的 Hexo 插件、渲染器、生成器、部署器以及其他前端/Node 库，并说明用途、来源位置与注意事项，便于后续维护升级。

数据来源
- package.json（Hexo 8.1.1）
- 主题与配置：`themes/fluid/`、`_config.yml`、`_config.fluid.yml`
- CI 环境：Node 22 + pnpm（.github/workflows/hexo-deploy.yml）

## 概览

- Hexo 核心：`hexo@^8.1.1`、`hexo-cli`（通过 npx/脚本调用）、`hexo-server`（本地预览）。
- 主题：Fluid（EJS 模板），相关定制在 `_config.fluid.yml`。
- 渲染器：EJS、Marked、Pug、Sass、Stylus。
- 生成器：index、archive、category、tag。
- 部署器：`hexo-deployer-git`（可用于本地/备用；CI 侧实际用 gh-pages Action）。
- 功能插件：文章加密、分页、标签（DPlayer）。
- 前端/Node 库：highlight.js、moment、nunjucks、sass、dplayer、css。

## 包-功能-入口对照表（快速检索）

| 包 | 类别 | 主要用途 | 配置/入口位置 | 当前使用情况 |
|---|---|---|---|---|
| hexo | 核心 | 静态站点生成核心 | `package.json` 脚本（build/clean/deploy/server） | 必用 |
| hexo-server | 核心 | 本地预览服务 | `pnpm exec hexo s` | 需本地预览时使用 |
| hexo-renderer-marked | 渲染器 | Markdown → HTML | 站点默认渲染 | 必用 |
| hexo-renderer-ejs | 渲染器 | EJS 模板渲染 | `themes/fluid/layout/*.ejs` | 主题必用 |
| hexo-renderer-stylus | 渲染器 | Stylus → CSS | `themes/fluid/source/css/**/*.styl` | 主题使用 |
| hexo-renderer-sass + sass | 渲染器 | Sass/SCSS → CSS | 若使用 `.scss` 资源 | 可用（按需） |
| hexo-renderer-pug | 渲染器 | Pug 模板渲染 | 自定义页面/主题扩展 | 目前未用（可移除） |
| hexo-generator-index | 生成器 | 主页路由与页面 | 默认启用 | 必用 |
| hexo-generator-archive | 生成器 | 归档页 | 默认启用 | 必用 |
| hexo-generator-category | 生成器 | 分类页 | 默认启用 | 必用 |
| hexo-generator-tag | 生成器 | 标签页 | 默认启用 | 必用 |
| hexo-pagination | 工具 | 列表分页支持 | 被生成器使用 | 内部使用 |
| hexo-deployer-git | 部署器 | 将 `public/` 推到分支 | `_config.yml: deploy` | 本地可用；CI 用 gh-pages |
| hexo-blog-encrypt | 插件 | 文章加密 | front‑matter 与 `_config.yml`（若配置） | 按需使用 |
| hexo-tag-dplayer | 标签 | Markdown 中插入视频播放器 | `source/_posts/测试插入视频.md` 示例 | 已用 |
| dplayer | 前端库 | HTML5 播放器运行时 | 由 tag 插件或自定义脚本加载 | 已用（随标签） |
| highlight.js | 前端库 | 代码高亮 | `_config.fluid.yml: code.highlight.lib` | 已用 |
| moment | 前端库 | 日期格式化 | 主题与插件内部 | 已用（间接） |
| nunjucks | 模板库 | 可用于脚本/自定义模板 | 暂无入口 | 未用（可移除） |
| css | Node 库 | CSS 解析工具 | 脚本/插件可用 | 未直接使用 |
| LXGW WenKai WebFont | 字体 | 霞鹜文楷中文字体 | `/css/lxgw-wenkai.css` + `_config.fluid.yml.font.font_family` | 已用 |

## 运行与工具链

| 组件 | 版本/来源 | 用途 | 备注 |
|---|---|---|---|
| Node | 22（CI） | CI 构建与部署 | actions/setup-node@v4，cache: pnpm |
| 包管理器 | pnpm@9（CI） | 安装依赖与缓存 | pnpm/action-setup@v4 |
| Hexo | ^8.1.1 | 静态站点生成器核心 | `package.json.hexo.version` 声明 |
| hexo-server | ^3.0.0 | 本地预览服务 | `pnpm exec hexo s` |

## 渲染器（Renderer）

| 包 | 作用 | 在本项目中的使用 | 备注 |
|---|---|---|---|
| hexo-renderer-marked | Markdown → HTML | 渲染文章与页面 | 与主题配合，受 `_config.yml` 与主题配置影响 |
| hexo-renderer-ejs | EJS 模板 | 主题 Fluid 使用 `.ejs` 布局 | 主题布局位于 `themes/fluid/layout/*.ejs` |
| hexo-renderer-pug | Pug 模板 | 备用（当前主题未使用） | 可用于自定义页面/主题扩展 |
| hexo-renderer-sass | Sass/SCSS → CSS | 支持 Sass 资源 | 结合 `sass` 运行时 |
| hexo-renderer-stylus | Stylus → CSS | 支持 Stylus 资源 | Fluid 主题包含 stylus 变量文件 |

## 生成器（Generator）

| 包 | 作用 | 输出 |
|---|---|---|
| hexo-generator-index | 生成首页 | posts 列表路由与页面 |
| hexo-generator-archive | 生成归档页 | 按日期聚合 |
| hexo-generator-category | 生成分类页 | 按分类聚合 |
| hexo-generator-tag | 生成标签页 | 按标签聚合 |

## 部署（Deployer）

| 包 | 作用 | 使用方式 |
|---|---|---|
| hexo-deployer-git | 将 `public/` 推送至 Git 分支 | 本地可 `hexo d`；CI 实际使用 `peaceiris/actions-gh-pages@v3` 将 `public/` 发布到 `master` |

## 功能插件与标签

| 包 | 作用 | 在本项目中的位置/用法 | 备注 |
|---|---|---|---|
| hexo-blog-encrypt | 文章加密 | 通过 front‑matter 指定加密文章 | 参见插件文档配置密钥与提示语 |
| hexo-tag-dplayer | Markdown 标签 `{% dplayer ... %}` | `source/_posts/测试插入视频.md` 示例 | 依赖前端播放器 dplayer（见下） |
| hexo-pagination | 分页工具 | 被生成器用于分页 | 内部工具库 |

## 前端/Node 辅助库

| 包 | 作用 | 主题/配置关联 | 备注 |
|---|---|---|---|
| highlight.js | 代码高亮 | `_config.fluid.yml` 中 `code.highlight.lib: highlightjs` | 主题会注入对应 CSS/JS |
| moment | 日期处理 | 主题与插件格式化时间 | 可被模板 helper 使用 |
| nunjucks | 模板引擎库 | 作为依赖存在 | 当前主题为 EJS，nunjucks 可用于脚手架/脚本 |
| sass | Sass 编译运行时 | 搭配 `hexo-renderer-sass` | CI/本地均可用 |
| dplayer | HTML5 播放器 | 与 `hexo-tag-dplayer` 配合 | 通过 CDN 或本地资源加载 |
| css | CSS 解析器 | 脚本/插件可用 | 非 Hexo 核心必需 |

## 主题与外部资源

- 主题：Fluid（EJS 模板，支持 highlight.js 与多种前端增强）。
- 字体：已通过 `/css/lxgw-wenkai.css` 引入「霞鹜文楷」WebFont，并在 `_config.fluid.yml.font.font_family` 指定为首选字体族。
- 静态前缀/CDN：主题 `static_prefix` 可指向公共 CDN（见 `_config.fluid.yml`），保持与主题建议版本一致。

## 配置入口对照

| 功能 | 主要配置文件 | 关键键位 |
|---|---|---|
| 站点与路径 | `_config.yml` | `url`, `root`, `source_dir`, `public_dir` |
| 主题样式与字体 | `_config.fluid.yml` | `font`, `color`, `custom_css`（已添加 `/css/lxgw-wenkai.css`） |
| 代码高亮 | `_config.fluid.yml` | `code.highlight.lib: highlightjs` 及样式主题 |
| 部署（Hexo） | `_config.yml` / `_config.fluid.yml` | `deploy`（git），但 CI 使用 actions-gh-pages |
| CI/CD | `.github/workflows/hexo-deploy.yml` | Node 22 + pnpm + 缓存，发布到 `master` |

## 升级与兼容性建议

- Hexo 8 与 Node 22：已在 CI 侧验证；本地建议 Node 18+。
- 渲染器选择：若仅用 EJS + Markdown，可移除未使用的 `pug`/`nunjucks` 渲染器以减少体积与依赖面。
- 代码高亮：如需 PrismJS 功能，可改为 `prismjs` 流程（需调整主题配置与资源）。
- DPlayer：推荐使用稳定 CDN 与 HTTPS 媒体源，避免混合内容导致页面受阻。
- 文章加密：谨慎管理密钥与回退；前端加密属轻量保护，勿存放敏感信息。

## 实际依赖快照（来自 package.json）

```json
{
  "dependencies": {
    "css": "^3.0.0",
    "dplayer": "^1.26.0",
    "hexo": "^8.1.1",
    "hexo-blog-encrypt": "^3.0.13",
    "hexo-deployer-git": "^4.0.0",
    "hexo-generator-archive": "^2.0.0",
    "hexo-generator-category": "^2.0.0",
    "hexo-generator-index": "^4.0.0",
    "hexo-generator-tag": "^2.0.0",
    "hexo-pagination": "^1.0.0",
    "hexo-renderer-ejs": "^2.0.0",
    "hexo-renderer-marked": "^7.0.1",
    "hexo-renderer-pug": "^3.0.0",
    "hexo-renderer-sass": "^0.5.0",
    "hexo-renderer-stylus": "^3.0.1",
    "hexo-server": "^3.0.0",
    "hexo-tag-dplayer": "^0.3.3",
    "hexo-util": "^4.0.0",
    "highlight.js": "^11.11.1",
    "moment": "^2.30.1",
    "nunjucks": "^3.2.4",
    "sass": "^1.93.3"
  }
}
```
