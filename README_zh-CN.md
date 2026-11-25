# 微博媒体下载器 (Gopeed 扩展)

[English](README.md) | 简体中文

一个零依赖的纯 JS [Gopeed](https://gopeed.com/) 扩展，自动解析微博视频、微博多图帖、Live Photo（实况照片）、图文混排帖及微博 CDN 直链，并自动注入所需的 `Referer: https://weibo.com/` 请求头。

## 功能特性

- **最高支持 4K (2160p) 视频：** 解析微博视频号 / TV 页面（`weibo.com/tv/show/...`、`video.weibo.com/show?fid=...`）和微博正文内嵌视频，自动获取全清晰度列表与文件大小。
- **多图原图与实况照片：** 按发帖顺序提取微博原图（优先 `largest`），以微博原始图片 ID（`<pid>.jpg` / `.gif`）命名，并支持下载 Live Photo 实况短视频（`.mov` / `.mp4`）。
- **图文混排与转发帖：** 完整解析 `mix_media_info` 图文视频混排帖子；若转发帖自身无媒体，自动回退解析原微博中的视频/图片。
- **私密 / 仅粉丝可见 / 会员视频支持：** 提供可选的 **Weibo Cookie** 设置（自动附带 `Cookie` 与 `X-XSRF-TOKEN` 请求头），支持已登录账号有权限查看的私密、仅粉丝/好友圈可见及部分 1080p/4K 视频；留空时会自动获取访客 Cookie 解析公开内容。
- **短链接还原：** 自动跟踪多层 `t.cn` 短链跳转并解包访客/外链中转地址（`passport.weibo.com`、`weibo.cn/sinaurl`）；若短链指向微博以外的网站，则自动交回给 Gopeed 默认处理。
- **CDN 直链自动补全请求头：** 直接粘贴 `*.weibocdn.com` 或 `*.sinaimg.cn` 直链时，自动添加 `Referer: https://weibo.com/` 和 `User-Agent`。
- **纯 JS 运行：** 专为 Gopeed 内置 `goja` 引擎编写，无需 Node.js 或编译构建步骤。

## 支持的链接格式

| 类型 | 示例链接 |
| :--- | :--- |
| 微博视频号 / TV | `https://weibo.com/tv/show/1034:...` |
| 微博视频页面 | `https://video.weibo.com/show?fid=1034:...`、`https://h5.video.weibo.com/show?fid=1034:...` |
| 旧版 TV 帖子链接 | `https://weibo.com/tv/v/<postId>` |
| 微博正文（视频 / 多图 / 混排） | `https://weibo.com/<uid>/<postId>`、`https://weibo.com/detail/<id>`、`https://m.weibo.cn/detail/<id>`、`https://m.weibo.cn/status/<id>` |
| 客户端分享链接 | `https://share.api.weibo.cn/share/<uid>,<postId>.html` |
| 微博短链接 | `http://t.cn/...` / `https://t.cn/...` |
| 微博视频 / 图片 CDN 直链 | `https://*.weibocdn.com/...` / `https://*.sinaimg.cn/...` |

> 不支持用户主页或信息流页面（如 `weibo.com/u/...` 或 `weibo.com/<uid>`），请粘贴单条微博或视频链接。

## 安装方法

### 方式一：通过 Git 仓库安装（推荐普通用户）
1. 打开 **Gopeed** → **扩展** 页面。
2. 在安装输入框中填入本项目的 Git 仓库地址，点击 **安装**。

### 方式二：本地开发者模式安装
1. 打开 **Gopeed 桌面端** → **扩展** 页面。
2. 保持顶部的 Git URL 输入框**为空**，**连续快速点击 5 次**输入框旁的 **安装** 按钮开启开发者模式（随后会出现本地文件夹选择按钮）。
3. 选择包含 `manifest.json` 和 `index.js` 的项目根目录。

> **提示：** 当 `manifest.json` 发生变更（如新增配置项或 URL 匹配规则）后，请在 Gopeed 中重新加载/重新安装本地目录或重启 Gopeed 使其生效。

## 扩展设置

前往 **扩展** → 点击 **Weibo Media Downloader** 旁的 **设置（齿轮图标）**：

| 设置项 | 默认值 | 说明 |
| :--- | :--- | :--- |
| **Preferred Quality** (`quality`) | `Best Available` | 自动选择最高画质、限制最高清晰度（`2160p` / `1080p` / `720p` / `480p` / `360p`），或在 Gopeed 任务窗口中列出全部清晰度供手动勾选。 |
| **Live Photo Mode** (`livePhoto`) | `Both Image & Video Clip` | 控制实况照片下载行为：`同时下载图片与视频`、`仅图片` 或 `仅视频片段`。 |
| **Weibo Cookie (Optional, for Private / 4K)** (`cookie`) | *(留空)* | 用于私密、仅粉丝/好友圈可见、会员或部分 1080p/4K 视频。留空时公开内容将自动使用访客 Cookie。 |
| **Custom User-Agent (Optional)** (`userAgent`) | *(留空)* | 自定义 `User-Agent`，留空则使用内置的桌面端 Chrome UA。 |

## 如何获取 Weibo Cookie（用于私密 / 仅粉丝可见 / 4K 视频）

1. 在浏览器打开 [weibo.com](https://weibo.com) 并登录你的微博账号。
2. 按 `F12` 打开浏览器开发者工具：
   - **快捷方式（仅复制 `SUB`）：** 切换到 **Application（应用）** / **Storage（存储）** → **Cookies** → `https://weibo.com`，找到 `SUB` 字段复制其值，直接粘贴到扩展设置的 **Weibo Cookie** 中即可。
   - **完整 Cookie 请求头（推荐用于严格权限帖子）：** 切换到 **Network（网络）** 面板刷新页面，点击一条 `weibo.com/ajax/statuses/show` 请求，复制请求头中的完整 `Cookie` 值粘贴到设置中（若包含 `XSRF-TOKEN`，扩展会自动补充 `X-XSRF-TOKEN` 请求头）。

> **隐私说明：** 你的 Cookie 仅保存在本地 Gopeed 扩展设置中，且**仅会**发送至 `weibo.com` / `weibo.cn` 接口（绝不会写入日志，也不会发送到 `weibocdn.com` / `sinaimg.cn` CDN 域名或任何第三方服务器）。

## 常见问题排查

- **提示 `Login required` 或 `Cookie has expired`：** 该微博或视频为私密、好友圈、仅粉丝可见或已过期，请填入有查看权限的已登录账号 Cookie。
- **部分视频解析为 `.m3u8`：** 当微博只提供 HLS 切片流（例如直播回放或特殊视频）而无 MP4 直链时，扩展会输出 `.m3u8` 文件并记录警告日志。
- **查看调试日志：** Gopeed 会在其安装/数据目录下的 `logs/extension.log` 输出扩展日志（开发者模式下包含 `DBG` 级别日志）。

## 开源协议

[MIT](LICENSE)
