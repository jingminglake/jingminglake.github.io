# Task: Google Analytics UA → GA4 迁移

## 状态: 已完成 (2026-04-03)

## 优先级: P0

## 背景

当前博客使用 Google Analytics Universal Analytics (UA-150811080-1)，该版本已于 2024年7月完全停止数据收集。博客目前等于没有任何访问统计。

## 涉及文件

- `_includes/footer.html` 第186-202行（当前 UA 代码）
- `_config.yml` 第72-73行（ga_track_id 配置）

## 当前代码

```html
<!-- footer.html 186-202行 -->
{% if site.ga_track_id %}
<script>
    var _gaId = '{{ site.ga_track_id }}';
    var _gaDomain = '{{ site.ga_domain }}';
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;...})(window,document,'script','//www.google-analytics.com/analytics.js','ga');
    ga('create', _gaId, _gaDomain);
    ga('send', 'pageview');
</script>
{% endif %}
```

## 迁移步骤

### 步骤一：创建 GA4 Property（需用户操作）

1. 登录 Google Analytics: https://analytics.google.com
2. 管理 → 创建资源（Property）→ 选择 GA4
3. 输入博客信息，获取 Measurement ID（格式: G-XXXXXXXXXX）

### 步骤二：更新 _config.yml

```yaml
# 旧配置
ga_track_id: 'UA-150811080-1'
ga_domain: auto

# 新配置
ga_track_id: 'G-XXXXXXXXXX'  # 替换为实际的 GA4 Measurement ID
# ga_domain 字段可删除，GA4 不需要
```

### 步骤三：替换 footer.html 中的统计代码

将第186-202行替换为:

```html
<!-- Google Analytics (GA4) -->
{% if site.ga_track_id %}
<script async src="https://www.googletagmanager.com/gtag/js?id={{ site.ga_track_id }}"></script>
<script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', '{{ site.ga_track_id }}');
</script>
{% endif %}
```

### 步骤四：验证

1. push 到 master，等待 GitHub Pages 构建
2. 打开博客页面，F12 → Network → 搜索 "gtag" 或 "google"，确认请求发出
3. 在 GA4 后台 → 实时报告中确认数据到达

## 注意事项

- GA4 的 Measurement ID 格式是 `G-` 开头，不是 `UA-` 开头
- 旧的 UA 数据不会迁移到 GA4，GA4 是全新的数据起点
- `ga_domain` 配置项可以保留但 GA4 不使用，建议清理掉
