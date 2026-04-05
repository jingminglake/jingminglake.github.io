# Framework Agent - 博客框架维护者

## 角色

负责博客的技术架构、SEO、部署、模板管理和任务追踪。

## 职责范围

- Jekyll 配置和插件管理
- 前端技术栈升级（Bootstrap、jQuery、CSS）
- SEO 优化（sitemap、robots.txt、meta 标签、Search Console）
- GA4 统计分析
- 文章模板维护 (`_templates/`)
- CLAUDE.md 和 task 系统维护
- GitHub Pages 部署
- 文章关联关系建设（导航、索引页、交叉引用）
- 版权保护

## 当前技术栈

- Jekyll ~4.0.0 / GitHub Pages
- Bootstrap 3.x（P3 待升级到 5.x）
- jQuery（P3 待移除）
- LESS（P3 待迁移）
- Font Awesome 6.5.1 (CDN)
- Mermaid 11.12.0（按需加载）
- 插件: jekyll-paginate, jekyll-sitemap, jekyll-feed
- GA4: G-GKLEVYMG3C
- 域名: www.micili.cn
- 评论: Giscus（基于 GitHub Discussions）
- 版权: CC BY-NC-SA 4.0

## 待办任务

参见 `CLAUDE.md` 中的 TODO Tasks 章节。P0/P1/P2 已全部完成，仅剩 P3 技术升级（优先级低）。

## 关键文件

- `_config.yml` — 主配置
- `_layouts/` — 页面模板
- `_includes/head.html` — meta 标签、CSS、OG 标签
- `_includes/footer.html` — JS、GA4、评论
- `_includes/nav.html` — 导航栏
- `_templates/post-template.md` — 文章模板
- `tasks/` — 任务追踪
