# CLAUDE.md - 亚雄的博客 (Yaxiong's Blog)

## 项目概述

这是一个基于 Jekyll 的个人博客，托管在 GitHub Pages 上，自定义域名为 www.micili.cn。
博客内容以中文为主，涵盖圣经研读、编程技术、个人成长等主题。

## 技术栈

- **静态站点生成器**: Jekyll ~4.0.0
- **Markdown 处理**: kramdown (GFM)
- **代码高亮**: rouge
- **CSS 框架**: Bootstrap 3.x + 自定义 Hux Blog 主题 (LESS 源码在 `/less/`)
- **插件**: jekyll-paginate
- **评论系统**: Disqus (用户名: jingming)
- **分析**: Google Analytics (UA-150811080-1)
- **托管**: GitHub Pages，自定义域名 www.micili.cn (CNAME)

## 目录结构

```
_config.yml          # Jekyll 主配置
_layouts/            # 页面模板 (default, post, page, keynote)
_includes/           # 可复用组件 (head, nav, footer)
_posts/              # 博客文章 (~171 篇)
css/                 # 编译后的样式
less/                # LESS 样式源码
js/                  # JavaScript 文件
img/                 # 图片资源
pwa/                 # PWA 配置
about.html           # 关于页面
index.html           # 首页 (分页)
tags.html            # 标签归档页
404.html             # 错误页面
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
- **读圣经** (最大分类): 马可福音、约翰福音、创世纪、腓立比书、提摩太后书、雅歌等
- **Leetcode**: 算法题解
- **编程语言**: Java、JavaScript、Ruby
- **后端** / **系统设计** / **设计模式**: 技术文章
- **心路历程** / **道路真理生命** / **音乐**: 个人与信仰

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
