# website — DEV_NOTES

## 这是什么

个人网站。首页 + 作品集 + 画廊 + 留言板。

## 当前状态

设计方向已确定：**复古 Web / 旧互联网美学**（Netscape 4.7 风格窗口外壳、scroll-snap 全页导航、Win98 凸起边框、宝丽来照片框、marquee、visitor counter、88×31 按钮）。

首页源头是 `public/index.html`（从 `hein.oldweb.bundled.html` 解包而来的纯静态 HTML）。**不再用 302 跳转壳**——`src/pages/index.astro` 和 `public/oldweb.html` 已删除，避免路由/构建冲突。

页面是自包含的单文件 SPA，内部 scroll-snap 6 个分页（home/about/art/news/guestbook/links），不依赖 Astro 路由。

### 解包说明（重要）

原 `hein.oldweb.bundled.html` 是自解包格式：模板 HTML 存成 JSON 转义字符串 + base64 资源，运行时再注入 DOM。这种格式无法正常编辑（改 Giscus 会动到转义串、内联脚本互相绑死）。

已用脚本解包成可编辑文件：
- `public/index.html` — 解转义后的纯 HTML（首页本体，直接改这个）
- `public/_image-slot.js` — 从 manifest 解出的 `<image-slot>` 占位组件脚本（31KB，图片占位用，无 omelette 运行时则只读）

`hein.oldweb.bundled.html` 现为**历史存档**，不要再改它。改首页改 `public/index.html`。

### 留言板（Giscus）

已从 localStorage 换成 Giscus（GitHub Discussions）：
- 仓库 `1756141021/hein-site`，repoId `R_kgDOSq7_iw`
- category `Announcements`（防访客乱建 discussion），categoryId `DIC_kwDOSq7_i84C-DtJ`
- mapping `specific`，term `guestbook`（整个留言板共用一条 discussion）
- theme `light`，lang `zh-CN`
- **待办：装 giscus GitHub App**（https://github.com/apps/giscus，选 hein-site 仓库）。装之前 iframe 显示 "giscus is not installed"。

### 图片（固定文件名约定）

10 个 `<image-slot>` 已全部填图。`<image-slot>` 吃 `src` 属性（脚本第 605 行 `srcAttr` 直通），所以换图 = 按同名替换 `public/images/` 下的文件，**无需改代码**：

| 文件名 | 位置 |
|--------|------|
| `hero.png` | 首页主视觉相框 |
| `avatar.png` | 关于页头像 |
| `art-1.png` ~ `art-8.png` | 画廊 bento 8 格 |

起始图是 4 张角色原图压到网页尺寸（1200/900px）后铺上去的占位，随时可换。
注意：拖拽填图只在原 bundler 的 omelette 运行时有效，静态站上是只读的，只能用 `src`/换文件。

### 访问方式（重要）

- **线上**：https://heins-corner.xiluozicanyue.workers.dev ✓（Workers Builds，连 GitHub 自动部署）
- **生产构建**：`npm run build` → `dist/`（public/ 原样拷贝，0 个 astro 页面）。`public/index.html` 在根，`/` 直接出页面。
- **本地 Astro dev**：dev server 不把 `public/index.html` 映射到裸 `/`（会 404），需走 `/index.html`。这是 dev 特性，不影响生产。
- dev 端口默认 4321，被占用时自动漂移（4322…）。启动前用 `Get-NetTCPConnection -LocalPort 4321` 清掉僵尸进程。

### 部署（Cloudflare Workers Builds，自动）

- 项目名 `heins-corner`，账户 ID `9794cbd437041b31ec5df7d0eadab590`，账户子域 `xiluozicanyue`
- **已连 GitHub 自动部署**：Cloudflare 后台 Workers & Pages → 连 `hein-site` 仓库。以后 `git push origin master` 自动重新部署，无需 token、无需命令行。
- 配置：`wrangler.jsonc`（`assets.directory = ./public`），Build command 留空，Deploy command `npx wrangler deploy`。纯静态资源，不跑构建。
- 网址是 `*.workers.dev`（新版 Workers 流程），不是 `.pages.dev`。之前 CLI 直传建的 `heins-corner` Pages 项目可删（已被这个取代）。
- **历史踩坑**：wrangler OAuth 登录在本机走不通——localhost 回调被解析成 IPv6 ::1 但 wrangler 监听 127.0.0.1，且 Cloudflare 同意页加载要 ~2 分钟超过 wrangler 超时；脚本直接打 dash.cloudflare.com/oauth2/token 撞 JS 反爬挑战。最终改 GitHub 自动部署绕开全部。

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
6. 解包 bundled HTML 为可编辑源；留言板接入 Giscus

## 文件说明

| 文件 | 说明 |
|------|------|
| `public/index.html` | **首页本体，直接改这个**（已解包的纯 HTML） |
| `public/_image-slot.js` | `<image-slot>` 占位组件脚本（解包产物） |
| `hein.oldweb.bundled.html` | 历史存档（bundled 原件），不要再改 |
| `src/pages/_index.astro.bak` | 暗色版首页备份 |
| `src/components/` | 暗色版组件（Nav/Footer/WorkCard 等），当前未使用 |
| `src/styles/global.css` | 已改为复古配色 tokens，暗色版已覆盖 |
| `prototypes/` | 8 版设计原型存档 |
| `public/images/hero/` | 4 张角色原图 + 眼部裁切版 |

## 待办

- [x] ~~装 giscus GitHub App~~（已装，留言板生效）
- [x] ~~填充 image-slot 占位符~~（10 个位已填，固定文件名约定）
- [ ] 从 ComfyUI output 精选实际作品替换起始图
- [x] ~~接入 Giscus~~
- [x] ~~部署到 Cloudflare Pages~~（heins-corner.pages.dev）
- [x] ~~解包 bundled HTML~~（已解成 public/index.html）
- [ ]（可选）Cloudflare 后台连 GitHub 仓库做 push 自动部署
- [ ]（安全）删掉临时用过的 Cloudflare API token
