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
agents/              # 角色上下文文档
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

## 关键文件

- `_config.yml` — 主配置
- `_layouts/` — 页面模板
- `_includes/head.html` — meta 标签、CSS、OG 标签
- `_includes/footer.html` — JS、GA4、评论、版权声明
- `_includes/nav.html` — 导航栏
- `_templates/post-template.md` — 文章模板
- `tasks/` — 任务追踪

## 可用头图

技术类文章常用: `post-bg-desk.jpg`, `post-bg-hacker.jpg`, `post-bg-digital-native.jpg`, `post-bg-keybord.jpg`
通用: `post-bg-2015.jpg`, `post-bg-map.jpg`

## TODO Tasks

### P0 - 立即执行（全部完成）

- [x] Google Analytics UA → GA4 迁移（Measurement ID: G-GKLEVYMG3C）
- [x] SEO 优化（sitemap、robots.txt、OG标签、description、feed、Search Console 已提交）
- [x] 删除 IE8 兼容代码（html5shiv + respond.js）
- [x] 删除 FastClick（现代浏览器已不需要）

### P1 - 近期执行（全部完成）

- [x] 建立文章关联关系（详见 `tasks/P1-post-cross-references.md`）
  - 系列文章上一篇/下一篇导航已完成，缺日期文件已移至 _drafts/，互相引用和索引页暂不做（tags页已覆盖）
- [x] Disqus → Giscus 迁移（基于 GitHub Discussions，无广告，加载快）
  - 文件: `_layouts/post.html`, `_layouts/keynote.html`, `about.html`, `_config.yml`
- [x] Font Awesome 4.6.3 → 6.5.1 升级
  - 文件: `_includes/head.html` CDN 链接 + `_includes/footer.html` 图标类名

### P2 - 计划执行（全部完成）

- [x] 添加版权保护声明 CC BY-NC-SA 4.0（详见 `tasks/P2-copyright-notice.md`）

### P3 - GitHub Actions（CI/CD 增强）

- [ ] 自定义 Jekyll 构建部署
  - 用 Actions 替代 GitHub Pages 内置构建，支持任意插件，构建失败有明确报错
  - 文件: `.github/workflows/jekyll.yml`
- [ ] Broken Link Checker（死链检测）
  - 自动检测站内/站外死链，154+ 篇文章需要持续维护链接有效性
- [ ] Lighthouse CI（性能/SEO 审计）
  - 每次 push 自动跑 Lighthouse，监控性能、SEO、可访问性评分
- [ ] HTML Proofer（HTML 校验）
  - 检查生成 HTML 合法性，检测缺失 alt 标签、空链接等
- [ ] 图片压缩
  - push 新图片时自动压缩优化，减少 `img/` 目录体积
- [ ] Markdown Lint
  - 检查文章 Markdown 格式一致性，确保 front matter 字段完整
- [ ] 定时重建（Scheduled Build）
  - 用 cron 定时触发构建，支持定时发布草稿

### P4 - 技术升级（优先级低，工作量大）

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
