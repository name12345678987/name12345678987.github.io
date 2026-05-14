# Hugo Theme Stack 本地手册笔记

来源：Stack 中文文档与 demo 配置。本文是项目内维护笔记，不是网页原文拷贝。

- 文档首页：https://stack.cai.im/zh/guide/
- Demo：https://demo.stack.cai.im/zh/
- 主题仓库：https://github.com/CaiJimmy/hugo-theme-stack
- Starter：https://github.com/CaiJimmy/hugo-theme-stack-starter

## 当前项目诊断

当前项目位于 `D:\codex\blog\myblog`，主题为 `stack`。

和 demo 差异较大的原因主要不是主题安装错误，而是配置和内容结构还没有按 Stack demo 的方式展开：

- 当前只有一个根级 `hugo.toml`，而 demo 使用 `config/_default/*.toml` 拆分配置。
- demo 的主要文章章节是 `content/post`，并配置 `mainSections = ["post"]`；当前项目文章在 `content/posts`，配置为 `mainSections = ["posts"]`。
- 当前 `content/posts/first-post.md` 是 `draft = true`，普通构建和发布时不会显示。
- 当前没有配置 Stack 推荐的左侧栏头像、副标题、社交链接、右侧 widgets。
- 当前没有创建 `layout: archives` 的归档页，也没有创建 `layout: search` 的搜索页。
- 当前评论配置使用了自定义的 `enableGiscus` 等字段，但 Stack v4 期望在 `[params.comments]` 和 `[params.comments.giscus]` 下配置。

如果目标是接近 demo，优先补齐配置，而不是直接改主题源码。

## 安装与版本

Stack 需要 Hugo extended 版本，因为主题使用 SCSS 和 TypeScript。

检查命令：

```powershell
..\hugo\hugo.exe version
```

当前项目里的 `D:\codex\blog\hugo\hugo.exe` 是 extended 版本，可以使用 Stack。

官方推荐使用 Hugo Module 或 starter 模板。当前项目是将主题放在 `themes/stack` 下，这也可以工作，但后续升级时要更小心。

## 不要直接改主题源码

小范围自定义时，优先在站点根目录覆盖主题文件：

- 主题文件：`themes/stack/layouts/_partials/head/custom.html`
- 覆盖位置：`layouts/_partials/head/custom.html`

同理，`layouts`、`assets`、`static` 都可以在站点根目录中覆盖。这样以后更新主题时，不容易丢改动。

## 推荐内容结构

Stack 推荐使用 Hugo page bundle：

```text
content/
  post/
    my-first-post/
      index.md
      image1.png
      image2.png
```

文章里的本地图片可以直接写：

```markdown
![Image 1](image1.png)
```

图片画廊也依赖 page bundle。多张图片连续放在 Markdown 中，主题会配合 PhotoSwipe 做画廊和缩放。

## 站点核心配置

Stack 主要配置在 `[params]` 下。

常用字段：

```toml
[params]
mainSections = ["post"]
rssFullContent = true
favicon = "img/avatar.png"
description = "站点描述"
```

说明：

- `mainSections` 决定首页和归档页读取哪些内容章节。
- demo 默认使用 `post`，所以如果文章目录叫 `posts`，要么改配置，要么改目录。
- `rssFullContent = true` 表示 RSS 输出全文。
- `favicon` 可以指向 `assets` 或 `static` 中的图片。

中文站点建议：

```toml
defaultContentLanguage = "zh"
hasCJKLanguage = true
```

## 侧边栏

左侧栏配置在 `[params.sidebar]`。

```toml
[params.sidebar]
compact = false
emoji = "✍️"
subtitle = "个人技术博客"
avatar = "img/avatar.png"
```

说明：

- `compact` 开启紧凑侧边栏。
- `emoji` 显示在头像上方。
- `subtitle` 显示在站点标题下方。
- `avatar` 可以是本地路径或 URL。本地文件推荐放在 `assets/img/avatar.png`。

## 菜单

Stack 有两个主要菜单：

- `menu.main`：左侧主菜单。
- `menu.social` 或配置文件中的 `[[social]]`：社交链接。

推荐通过内容页面 front matter 生成菜单，这样当前页面高亮更准确。

也可以在配置中写：

```toml
[[menu.main]]
name = "首页"
url = "/"
weight = 10
identifier = "home"

  [menu.main.params]
  icon = "home"
  newTab = false
```

说明：

- `weight` 越小越靠前。
- `params.icon` 会在 `assets/icons` 或主题图标目录下找同名 SVG。
- 如果需要自定义图标，把 SVG 放到站点根目录的 `assets/icons`。

社交链接示例：

```toml
[[social]]
identifier = "github"
name = "GitHub"
url = "https://github.com/your-name"

  [social.params]
  icon = "brand-github"
```

## 小部件

右侧栏 widgets 配置在 `[params.widgets]`。

demo 首页常见配置：

```toml
[params.widgets]
homepage = [
  { type = "search" },
  { type = "archives", params = { limit = 5 } },
  { type = "categories", params = { limit = 10 } },
  { type = "tag-cloud", params = { limit = 10 } },
]
page = [
  { type = "toc" }
]
```

可用 widgets：

- `search`：搜索框，需要创建 `layout: search` 页面。
- `archives`：归档列表，需要创建 `layout: archives` 页面。
- `categories`：分类列表。
- `tag-cloud`：标签云。
- `toc`：文章目录。
- `taxonomy`：通用分类法组件，可替代分类和标签组件。

`toc` 只在文章有标题时显示，并且需要同时满足：

- `[params.article].toc = true`
- 页面 front matter 未禁用 `toc`
- `widgets.page` 包含 `{ type = "toc" }`

## 归档页和搜索页

归档页需要单独创建内容页：

```text
content/page/archives/index.md
```

front matter 示例：

```toml
+++
title = "归档"
layout = "archives"
slug = "archives"
+++
```

搜索页需要：

```text
content/page/search/index.md
```

front matter 示例：

```toml
+++
title = "搜索"
layout = "search"
slug = "search"
+++
```

然后在菜单里加 `/search/` 和 `/archives/`。

## 自定义主页

默认主页是文章列表。

如果想让首页变成静态页面，创建：

```text
content/_index.md
```

并设置：

```toml
+++
title = "首页"
layout = "single"
+++
```

这样首页会渲染这个单页内容，而不是最近文章列表。

## 文章 Front Matter

Stack 支持这些常用字段：

```toml
+++
title = "文章标题"
date = 2026-05-14T10:00:00+08:00
description = "文章摘要"
image = "cover.jpg"
categories = ["技术"]
tags = ["Hugo", "博客"]
comments = true
license = true
math = false
toc = true
keywords = ["Hugo", "Stack"]
readingTime = true
+++
```

字段说明：

- `description`：页面描述，也会影响摘要和 SEO。
- `image`：特色图，可用于列表缩略图和分享图。
- `comments`：控制单篇文章是否显示评论。
- `license`：字符串、自定义许可证，或 `false` 隐藏。
- `math`：启用 KaTeX 数学公式。
- `toc`：控制目录。
- `keywords`：SEO 关键词。
- `readingTime`：控制阅读时间显示。

分类、标签页面还可以用 `style` 设置徽章样式：

```toml
+++
title = "Hugo"
style = { background = "#2d6cdf", color = "#fff" }
+++
```

## 文章配置

文章配置在 `[params.article]`：

```toml
[params.article]
headingAnchor = false
math = false
toc = true
readingTime = true

  [params.article.license]
  enabled = true
  default = "Licensed under CC BY-NC-SA 4.0"
```

Mermaid 配置：

```toml
[params.article.mermaid]
look = "classic"
lightTheme = "default"
darkTheme = "dark"
securityLevel = "strict"
htmlLabels = true
transparentBackground = false
```

只有文章中存在 mermaid 代码块时，相关脚本才会加载。

## 日期格式

日期格式在 `[params.dateFormat]`：

```toml
[params.dateFormat]
published = ":date_full"
lastUpdated = ":date_full"
```

也可以用 Go 日期格式：

```toml
published = "2006年1月2日"
lastUpdated = "2006年1月2日 15:04"
```

注意 Stack v4 更推荐和 Hugo 本地化日期格式配合使用。中文站点要设置正确的 `defaultContentLanguage`。

## 评论系统

Stack 支持：

- Cactus
- Comentario
- Cusdis
- Disqus
- DisqusJS
- Giscus
- Gitalk
- Remark42
- Twikoo
- utterances
- Vssue
- Waline

统一入口：

```toml
[params.comments]
enabled = true
provider = "giscus"
```

Giscus 示例：

```toml
[params.comments.giscus]
repo = "your-name/blog-comments"
repoID = ""
category = "General"
categoryID = ""
mapping = "pathname"
lightTheme = "light"
darkTheme = "dark"
reactionsEnabled = 1
emitMetadata = 0
inputPosition = "top"
lang = "zh-CN"
strict = 0
loading = "lazy"
```

当前项目里的 `enableGiscus`、`giscusRepo` 这类字段不是 Stack v4 的标准位置，建议迁移到上面的结构。

## 配色方案

配置在 `[params.colorScheme]`：

```toml
[params.colorScheme]
toggle = true
default = "auto"
```

`default` 可选：

- `light`
- `dark`
- `auto`

如果 `toggle = false`，站点按 `default` 固定显示。

## 图片处理

配置在 `[params.imageProcessing]`：

```toml
[params.imageProcessing]
autoOrient = false

  [params.imageProcessing.thumbnail]
  enabled = true

  [params.imageProcessing.content]
  enabled = true
  widths = [800, 1600, 2400]
```

说明：

- 本地 page bundle 图片会被 Hugo 自动处理为响应式图片。
- 图片多时，构建可能变慢。
- 外部图片也支持显示和缩放，但本地图片处理能力主要作用于 page bundle 资源。

## Open Graph

配置在 `[params.opengraph.twitter]`：

```toml
[params.opengraph.twitter]
site = ""
card = "summary_large_image"
```

`card` 常用值：

- `summary`
- `summary_large_image`

文章没有特色图时，v4 中旧版默认 Open Graph 图片配置已被弃用，优先通过文章 `image` 或新配置方式处理。

## 页脚

配置在 `[params.footer]`：

```toml
[params.footer]
since = 2026
customText = ""
```

`customText` 支持 HTML。

主题基于 GPL v3，官方要求不要移除页脚中的 “Theme Stack designed by Jimmy” 链接文字。

## 自定义 head / footer

可覆盖的常用 partial：

```text
layouts/_partials/head/custom.html
layouts/_partials/footer/custom.html
```

适合放：

- 自定义 CSS
- 字体引用
- 统计脚本
- 额外 meta 标签

如果要添加 CSS，优先放在 `assets/scss/custom.scss` 或覆盖主题提供的 custom 入口，而不是改主题主样式。

## 短代码

Stack 提供了一些内容短代码，包括：

- Bilibili 视频
- YouTube 视频
- Tencent 视频
- GitLab 代码片段
- quote
- video

示例格式通常是 Hugo shortcode：

```markdown
{{< bilibili VIDEO_ID PART_NUMBER >}}
```

具体参数后续改文章时再查对应 shortcode 文件：

```text
themes/stack/layouts/_shortcodes/
```

## Cookies

Stack v4 支持 GDPR cookie 同意横幅：

```toml
[params.cookies]
enabled = false
showSettings = true

  [params.cookies.categories]
  analytics = true
  functional = true
```

启用后，统计脚本和功能性 Cookie 会根据用户同意状态加载。

个人中文博客通常可以先保持关闭，除非接入 Google Analytics、Umami、广告或其他需要告知的第三方脚本。

## 升级到 v4 的注意点

- 对照新版 demo 配置迁移。
- 日期格式和语言代码需要按 Hugo 本地化规则调整。
- 中文语言代码建议使用主题当前支持的 `zh`，而不是旧习惯里的 `zh-cn`。
- 侧边栏菜单图标不再用 `.Pre` 配置，应改用 `params.icon`。
- widgets 支持更通用的 `taxonomy`。
- Mermaid 已经有原生配置。
- 新增 Comentario 评论支持。

## 让当前项目接近 demo 的最短路径

1. 将配置拆成 `config/_default/hugo.toml`、`params.toml`、`menu.toml`、`markup.toml`。
2. 统一文章目录：建议把 `content/posts` 改为 `content/post`，并设置 `mainSections = ["post"]`。
3. 把 `first-post.md` 改成 page bundle，取消 `draft = true`。
4. 添加 `assets/img/avatar.png`，配置 `[params.sidebar]`。
5. 添加 `content/page/archives/index.md` 和 `content/page/search/index.md`。
6. 配置首页 widgets：搜索、归档、分类、标签云。
7. 配置文章页 widgets：目录。
8. 把 Giscus 配置迁移到 `[params.comments.giscus]`。
9. 用 `..\hugo\hugo.exe server --buildDrafts` 本地预览。
10. 发布前用 `..\hugo\hugo.exe --gc --minify` 检查正式构建。

