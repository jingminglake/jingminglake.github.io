# CLAUDE.md - 亚雄的博客 (Yaxiong's Blog)

## 项目概述

这是一个基于 Jekyll 的个人博客，托管在 GitHub Pages 上，自定义域名为 www.micili.cn。
博客内容以中文为主，涵盖圣经研读、编程技术、个人成长等主题。

## 角色分工

Claude 作为博客管理者承担三个角色，各角色的详细上下文见对应文件：

1. **框架维护者** (`agents/framework/CONTEXT.md`) — 技术栈升级、SEO、部署、模板管理、任务追踪
2. **Leetcode 内容维护者** (`agents/leetcode/CONTEXT.md`) — 算法题解的写作、整理和维护
3. **圣经研读内容维护者** (`agents/bible/CONTEXT.md`) — 圣经笔记及信仰相关文章的写作和维护

用户通过"切换到 XX agent"来指定当前工作角色。

## 技术栈

- **静态站点生成器**: Jekyll ~4.0.0
- **Markdown 处理**: kramdown (GFM)
- **代码高亮**: rouge
- **CSS 框架**: Bootstrap 3.x + 自定义 Hux Blog 主题 (LESS 源码在 `/less/`)
- **图标库**: Font Awesome 6.5.1 (CDN)
- **图表**: Mermaid 11.12.0 (按需加载，front matter 设 `mermaid: true`)
- **插件**: jekyll-paginate, jekyll-sitemap, jekyll-feed
- **评论系统**: Giscus (基于 GitHub Discussions，repo: jingminglake/jingminglake.github.io)
- **分析**: Google Analytics GA4 (G-GKLEVYMG3C)
- **版权声明**: CC BY-NC-SA 4.0
- **托管**: GitHub Pages，自定义域名 www.micili.cn (CNAME)

## 目录结构

```
_config.yml          # Jekyll 主配置
_layouts/            # 页面模板 (default, post, page, keynote)
_includes/           # 可复用组件 (head, nav, footer)
_posts/              # 已发布文章 (~154 篇，按分类子目录组织)
_drafts/             # 草稿文章 (~10 篇，不会被发布)
_templates/          # 文章模板
tasks/               # 任务文档
css/                 # 编译后的样式
less/                # LESS 样式源码
js/                  # JavaScript 文件
img/                 # 图片资源
pwa/                 # PWA 配置
about.html           # 关于页面
index.html           # 首页 (分页)
tags.html            # 标签归档页
404.html             # 错误页面
feed.xml             # RSS 订阅源
sitemap.xml          # 站点地图 (自动生成)
robots.txt           # 搜索引擎爬虫规则
```

## 文章规范

### 文件命名

文章文件放在 `_posts/` 目录下，命名格式: `YYYY-MM-DD-标题.md`

### Front Matter 模板

```yaml
---
layout:     post
title:      "文章标题"
subtitle:   "副标题（可选）"
date:       YYYY-MM-DD
author:     Jingming
header-img: img/post-bg-xxx.jpg
catalog:    true
tags:
    - 标签1
    - 标签2
---
```

### 文章模板

模板文件位于 `_templates/post-template.md`，写新文章时以此为基础。

### 写作风格

- 语言: 中文为主，技术术语保留英文
- 结构: 用 `##` 作为大标题，`###` + 中文序号（一、二、三）作为分节
- 风格: 简洁直接，重实践和 SOP，适当加入个人理解和经验
- 文章按分类放在 `_posts/` 下的子目录中（如 `_posts/工作/`、`_posts/后端/`）

### 可用头图

技术类文章常用: `post-bg-desk.jpg`, `post-bg-hacker.jpg`, `post-bg-digital-native.jpg`, `post-bg-keybord.jpg`
通用: `post-bg-2015.jpg`, `post-bg-map.jpg`

### 内容分类

主要分类包括:
- **读圣经** (最大分类): 创世纪(50章)、马可福音(16章)、雅歌(8章)、约翰福音(4章)、腓立比书(4章)、提摩太后书(4章)
- **信仰相关**: 道路真理生命、信仰、关于服侍、读属灵书报
- **Leetcode**: 算法题解 (20篇)
- **编程语言**: Java、JavaScript、Ruby
- **技术**: 后端、系统设计、设计模式、前端、数据结构和算法、编程工具使用
- **工作**: 工作SOP、方法论
- **个人**: 心路历程、音乐、健身、学习、科普、英语

## TODO Tasks

### P0 - 立即执行

- [x] Google Analytics UA → GA4 迁移（Measurement ID: G-GKLEVYMG3C）
- [x] SEO 优化（sitemap、robots.txt、OG标签、description、feed、Search Console 已提交）
- [x] 删除 IE8 兼容代码（html5shiv + respond.js）
- [x] 删除 FastClick（现代浏览器已不需要）

### P1 - 近期执行

- [x] 建立文章关联关系（详见 `tasks/P1-post-cross-references.md`）
  - 系列文章上一篇/下一篇导航已完成，缺日期文件已移至 _drafts/，互相引用和索引页暂不做（tags页已覆盖）
- [x] Disqus → Giscus 迁移（基于 GitHub Discussions，无广告，加载快）
  - 文件: `_layouts/post.html`, `_layouts/keynote.html`, `about.html`, `_config.yml`
- [x] Font Awesome 4.6.3 → 6.5.1 升级
  - 文件: `_includes/head.html` CDN 链接 + `_includes/footer.html` 图标类名

### P2 - 计划执行

- [x] 添加版权保护声明 CC BY-NC-SA 4.0（详见 `tasks/P2-copyright-notice.md`）

### P3 - 技术升级（工作量较大，需仔细测试）

- [ ] Bootstrap 3.x → 5.x 升级
  - 影响: 5个布局文件，46+ class 需改名（col-xs-* → col-*、navbar-default 等）
  - 文件: `_layouts/`, `_includes/nav.html`, `_includes/footer.html`
- [ ] 去掉 jQuery，改用原生 JS
  - 影响: 3个自定义 JS 文件需重写
  - 文件: `js/hux-blog.js`, `js/jquery.nav.js`, `js/jquery.tagcloud.js`
- [ ] LESS → 现代 CSS 方案迁移
  - 文件: `less/` 目录，共 1,226 行

### 已完成

- [x] 生成 CLAUDE.md
- [x] 生成文章模板 `_templates/post-template.md`
- [x] 发布文章：优秀软件工程师日常开发流程SOP
- [x] 修复移动端滚动卡顿（overflow/touch-action/throttle/FastClick残留）
- [x] 正文和代码块字体改为 18px
- [x] 修复 SOP 文章 kramdown 有序列表渲染问题
- [x] 圣经系列文章同日排序修复（42篇重新分配日期）
- [x] 17个缺日期草稿文件移至 _drafts/

## 常用命令

```bash
# 本地开发预览
bundle exec jekyll serve

# 安装依赖
bundle install
```

## 部署

直接 push 到 master 分支，GitHub Pages 自动构建部署。无需 CI/CD 配置。

## 注意事项

- 修改 `_config.yml` 后需重启本地 Jekyll 服务
- 图片资源放在 `img/` 目录下，文章头图使用 `img/post-bg-*.jpg` 系列
- 标签出现次数 >= 1 时会显示在首页标签栏（`featured-condition-size: 1`）
- 每页显示 10 篇文章（分页配置）
