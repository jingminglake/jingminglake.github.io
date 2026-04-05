# CLAUDE.md - 亚雄的博客 (Yaxiong's Blog)

## 项目概述

这是一个基于 Jekyll 的个人博客，托管在 GitHub Pages 上，自定义域名为 www.micili.cn。
博客内容以中文为主，涵盖圣经研读、编程技术、个人成长等主题。

## 角色分工

Claude 作为博客管理者承担三个角色，各角色的详细上下文见对应文件：

1. **框架维护者** (`agents/framework/CONTEXT.md`) — 技术栈、目录结构、TODO任务、部署、常用命令、注意事项
2. **Leetcode 内容维护者** (`agents/leetcode/CONTEXT.md`) — 算法题解的写作、整理和维护
3. **圣经研读内容维护者** (`agents/bible/CONTEXT.md`) — 圣经笔记及信仰相关文章的写作和维护

用户通过"切换到 XX agent"来指定当前工作角色。

## 文章规范（通用）

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

### 内容分类

- **读圣经** (最大分类): 创世纪(50章)、马可福音(16章)、雅歌(8章)、约翰福音(4章)、腓立比书(4章)、提摩太后书(4章)
- **信仰相关**: 道路真理生命、信仰、关于服侍、读属灵书报
- **Leetcode**: 算法题解 (20篇)
- **编程语言**: Java、JavaScript、Ruby
- **技术**: 后端、系统设计、设计模式、前端、数据结构和算法、编程工具使用
- **工作**: 工作SOP、方法论
- **个人**: 心路历程、音乐、健身、学习、科普、英语
