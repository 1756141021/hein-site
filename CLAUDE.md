# website — 个人主页 & 作品集

## 技术栈

- **框架**: Astro
- **留言板**: Giscus (GitHub Discussions)
- **托管**: Cloudflare Pages
- **域名**: 暂用免费子域名 (xxx.pages.dev)

## 页面结构

| `/` | 首页：hero(60vh) + 作品网格 + 画廊预览 + 关于速览 + 留言板入口 |
| `/works/[slug]` | 项目详情页 (Content Collections) |
| `/gallery` | AI 画作展示 |
| `/guestbook` | Giscus 留言板 |
| `/about` | 关于页面 |

可扩展，未来加博客等。

### Hero

60vh，不浪费整屏。左侧文案 + 右侧**眼部裁切拼贴**（从多张 AI 画作截取眼部，CSS object-position + 不同尺寸 + 微小旋转，拼贴风格）。

素材来源（`E:\ComfyUI\ComfyUI\output\`）:
- 2026-05-26 `22-28-59` 红瞳狐耳
- 2026-05-27 `10-39-12` 蓝瞳女仆
- 2026-05-28 `15-36-19` 银瞳雪景
- 2026-05-27 `10-32-18` 冰蓝双马尾

### 领地感

- 文案有个性但稍正式（公开网站的分寸）
- AI 画作当环境装饰（section 间画作横条）
- 金色「」角标签名装饰
- "currently" 状态条
- Footer 个人签名

## 美学

### 核心

克制的华丽。冷黑底压暖金点缀，仔细看才发现的精致。原型基底是 `prototypes/C_dark_elegant.html`。

### 色彩

| Token | 值 | 说明 |
|-------|-----|------|
| --bg | #0A0A0F | 冷黑偏蓝 |
| --surface | #14141F | 卡片底色 |
| --text-1 | #E8E8EC | 主文字 |
| --text-2 | #8888A0 | 次要文字 |
| --accent | #D4A574 | 暖金 |
| --accent-hover | #E8BC8A | hover 提亮 |
| --line | rgba(255,255,255,0.08) | 分割线 |

红色不做主 accent。

### 字体

- Display: Cormorant Garamond, weight 300-500
- Body: Space Grotesk, weight 300-400
- Code/tags: JetBrains Mono

### 密度

不喜欢留白，喜欢页面的存在意义。紧凑但清晰，不是塞满。

- 卡片 gap: 1.25rem
- Section padding: clamp(4rem, 8vw, 8rem)，不超 8rem
- 版心 max-width: 1120px
- border-radius: 2px（几乎直角）

### 纹理

- 全局 noise overlay opacity 0.03
- Hero 极淡 radial-gradient glow（金色 opacity 6%），呼吸动画
- 不用渐变背景

### 动画

- 入场: translateY + opacity, 800-1400ms, cubic-bezier(0.23, 1, 0.32, 1)
- Hover: border 变亮 + translateY(-4px) + 淡金 box-shadow, 400ms
- 禁止: 弹跳、旋转、scale、视差滚动、打字机效果

### 红线

浅色底、圆角>4px、无衬线优雅层、色温偏暖棕、版心<900px、字重过粗——踩一条就出局。

### 卡片

- thumb(16:10) + title + desc + tags 三层满载
- 无图时深色渐变底 + 低透几何线条
- tags: pill (999px radius), accent border, mono 字体
