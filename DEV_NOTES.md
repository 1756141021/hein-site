# website — DEV_NOTES

## 这是什么

个人网站。首页 + 作品集 + 画廊 + 留言板。

## 当前状态

设计方向已确定：**复古 Web / 旧互联网美学**（Netscape 4.7 风格窗口外壳、scroll-snap 全页导航、Win98 凸起边框、宝丽来照片框、marquee、visitor counter、88×31 按钮）。

主文件是 `hein.oldweb.bundled.html`（由另一个 Claude Code 实例生成的 bundled 单文件，内含 base64 资源）。直接复制为 `public/index.html` 作为首页。**不再用 302 跳转壳**——`src/pages/index.astro` 和 `public/oldweb.html` 已删除，避免路由/构建冲突。

页面是自包含的单文件 SPA，内部 scroll-snap 6 个分页（home/about/art/news/guestbook/links），不依赖 Astro 路由。

图片占位符（`<image-slot>`）尚未填充实际图片。

### 访问方式（重要）

- **生产（Cloudflare Pages）**：`public/index.html` 部署到根，`/` 直接出页面 ✓
- **本地 Astro dev**：dev server 不把 `public/index.html` 映射到裸 `/`（会 404），需走 `/index.html`。这是 dev 特性，不影响生产。
- dev 端口默认 4321，被占用时自动漂移（4322…）。启动前用 `Get-NetTCPConnection -LocalPort 4321` 清掉僵尸进程。

## 技术栈

- Astro（静态站，仅做构建/部署容器；首页是纯静态 HTML）
- 实际页面是纯 HTML/CSS/JS 单文件
- 计划：Giscus 留言板、Cloudflare Pages 部署

## 设计历史

1. 原型阶段：8 版 HTML 原型（A-H），探索暗色优雅路线（`prototypes/`）
2. 暗色版实装：冷黑 #0A0A0F + 暖金 #D4A574，Astro 组件化，眼部裁切拼贴 hero
3. 用户否决暗色版排版（"好丑"），转向复古 Web 方向
4. 采用外部生成的 `hein.oldweb.bundled.html` 作为新基础
5. 清理架构：删掉 302 跳转壳，直接以 `public/index.html` 为首页

## 文件说明

| 文件 | 说明 |
|------|------|
| `hein.oldweb.bundled.html` | 复古版主文件（bundled，含 base64 资源），改源头改这个 |
| `public/index.html` | ↑ 的副本，即首页本体。改完 bundled 后需重新复制覆盖 |
| `src/pages/_index.astro.bak` | 暗色版首页备份 |
| `src/components/` | 暗色版组件（Nav/Footer/WorkCard 等），当前未使用 |
| `src/styles/global.css` | 已改为复古配色 tokens，暗色版已覆盖 |
| `prototypes/` | 8 版设计原型存档 |
| `public/images/hero/` | 4 张角色原图 + 眼部裁切版 |

## 待办

- [ ] 填充 image-slot 占位符（hero、avatar、gallery 8 格）
- [ ] 从 ComfyUI output 精选更多图片
- [ ] 接入 Giscus 替换本地 localStorage 留言板
- [ ] 部署到 Cloudflare Pages
- [ ] 决定是否把 bundled HTML 拆回 Astro 组件
