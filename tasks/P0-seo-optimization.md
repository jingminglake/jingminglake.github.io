# Task: SEO 优化

## 状态: 未开始

## 优先级: P0

## 背景

GA4 数据显示博客几乎没有来自搜索引擎的流量（Organic Search 为 0），99% 流量是 Direct 且停留 0 秒（爬虫/机器人）。经排查，博客缺少多项基础 SEO 配置。

## 问题清单

### 1. 无 sitemap.xml（严重）
- 搜索引擎无法高效发现和索引你的页面
- 需要安装 jekyll-sitemap 插件自动生成

### 2. 无 robots.txt（严重）
- 搜索引擎爬虫没有抓取指引
- 需要创建，指向 sitemap.xml

### 3. 网站描述未修改（严重）
- `_config.yml` 中 description 还是 Jekyll 默认模板文字：
  "Write an awesome description for your new site here..."
- 这段文字会直接出现在 Google 搜索结果中，严重影响点击率

### 4. 无 Open Graph 标签（中等）
- 文章链接在社交平台（微信、Twitter、Facebook）分享时没有标题、描述、图片预览
- 需要在 head.html 中添加 og:title、og:description、og:image 等标签

### 5. jekyll-feed 插件未激活（中等）
- Gemfile 中有 jekyll-feed 但 _config.yml 中被注释掉了
- 导致 feed.xml 不会生成，RSS 订阅不可用

### 6. keywords 关键词不够精准（轻微）
- 当前: "jingming的博客, jingming, cili, micili, C++, leetcode, 音乐, 圣经, Yaxiong"
- 没有包含博客实际的核心内容关键词，如"软件工程师"、"后端开发"、"圣经研读"等

## 修复步骤

### 步骤一：创建 robots.txt

在项目根目录创建 `robots.txt`:

```
User-agent: *
Allow: /
Sitemap: https://jingminglake.github.io/sitemap.xml
```

### 步骤二：安装 jekyll-sitemap 插件

在 `Gemfile` 中添加:
```ruby
gem "jekyll-sitemap"
```

在 `_config.yml` 的 plugins 中添加:
```yaml
plugins:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-feed
```

### 步骤三：修改 _config.yml 中的 description 和 keywords

```yaml
description: "亚雄的技术与信仰博客，分享软件工程师工作SOP、后端开发、系统设计、Leetcode题解、圣经研读等内容。"
keyword: "软件工程师, 后端开发, 系统设计, Leetcode, 圣经研读, SOP, Web开发, Ruby on Rails, 工作方法论"
```

### 步骤四：在 head.html 中添加 Open Graph 标签

在 `_includes/head.html` 中添加:

```html
<!-- Open Graph -->
<meta property="og:title" content="{{ page.title | default: site.title }}">
<meta property="og:description" content="{{ page.subtitle | default: site.description }}">
<meta property="og:url" content="{{ page.url | prepend: site.baseurl | prepend: site.url }}">
<meta property="og:site_name" content="{{ site.title }}">
<meta property="og:type" content="{% if page.layout == 'post' %}article{% else %}website{% endif %}">
{% if page.header-img %}
<meta property="og:image" content="{{ site.url }}{{ site.baseurl }}/{{ page.header-img }}">
{% endif %}
```

### 步骤五：激活 jekyll-feed 插件

已在步骤二中一起处理。

### 步骤六：提交 Sitemap 到搜索引擎

1. Google Search Console: https://search.google.com/search-console
   - 已有验证（head.html 中有 google-site-verification）
   - 登录后提交 sitemap: https://jingminglake.github.io/sitemap.xml
2. 百度站长平台: https://ziyuan.baidu.com
   - 注册并验证站点
   - 提交 sitemap

## 验证方式

1. 部署后访问 https://jingminglake.github.io/sitemap.xml 确认 sitemap 可访问
2. 访问 https://jingminglake.github.io/robots.txt 确认内容正确
3. 访问 https://jingminglake.github.io/feed.xml 确认 RSS 可用
4. 用 https://www.opengraph.xyz/ 测试文章链接的 OG 标签预览
5. 在 Google Search Console 中确认 sitemap 已提交且无错误
6. 2-4 周后观察 GA4 中 Organic Search 流量是否出现
