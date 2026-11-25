# Weibo Media Downloader (Gopeed Extension)

English | [简体中文](README_zh-CN.md)

A zero-dependency Pure-JS [Gopeed](https://gopeed.com/) extension that automatically resolves Weibo videos, image galleries, Live Photos, mixed-media posts, and direct Weibo CDN links into Gopeed download tasks with the required `Referer: https://weibo.com/` header.

## Features

- **Videos up to 4K (2160p):** Resolves Weibo TV / video links (`weibo.com/tv/show/...`, `video.weibo.com/show?fid=...`) and embedded post videos, extracting byte sizes and all available qualities.
- **Image Galleries & Live Photos:** Downloads full-resolution (`largest`) photos from Weibo posts using original Weibo image IDs (`<pid>.jpg` / `.gif`), plus Live Photo motion clips (`.mov` / `.mp4`).
- **Mixed Posts & Reposts:** Downloads all photos and videos from mixed `mix_media_info` posts in order, and falls back to quoted/reposted statuses when applicable.
- **Private / Follower-Only / VIP Support:** Includes an optional **Weibo Cookie** setting (sending `Cookie` + `X-XSRF-TOKEN` to Weibo APIs) for login-restricted, follower-only, private, or 4K videos, while automatically obtaining a guest visitor cookie for public posts when left blank.
- **Short Link Expansion:** Follows multi-hop `t.cn` redirects and unwraps Weibo visitor/redirect URLs (`passport.weibo.com`, `weibo.cn/sinaurl`); non-Weibo short links pass through to Gopeed normally.
- **Direct CDN Header Injection:** Automatically attaches `Referer: https://weibo.com/` and `User-Agent` to pasted `*.weibocdn.com` and `*.sinaimg.cn` links.
- **Pure JS Runtime:** Built directly for Gopeed's `goja` engine with no Node.js or build step required.

## Supported URLs

| Type | Example URL |
| :--- | :--- |
| Weibo TV Show | `https://weibo.com/tv/show/1034:...` |
| Weibo Video | `https://video.weibo.com/show?fid=1034:...`, `https://h5.video.weibo.com/show?fid=1034:...` |
| Legacy TV Post | `https://weibo.com/tv/v/<postId>` |
| Weibo Post (Video / Images / Mixed) | `https://weibo.com/<uid>/<postId>`, `https://weibo.com/detail/<id>`, `https://m.weibo.cn/detail/<id>`, `https://m.weibo.cn/status/<id>` |
| Mobile Share Link | `https://share.api.weibo.cn/share/<uid>,<postId>.html` |
| Short Link | `http://t.cn/...` / `https://t.cn/...` |
| Direct Video / Image CDN | `https://*.weibocdn.com/...` / `https://*.sinaimg.cn/...` |

> Profile and feed URLs (e.g., `weibo.com/u/...` or `weibo.com/<uid>`) are not supported—paste individual post or video links.

## Installation

### Option 1: Install via Git Repository (Recommended for Users)
1. Open **Gopeed** → **Extensions**.
2. Paste this repository's Git URL into the install box and click **Install**.

### Option 2: Install Locally (Developer Mode)
1. Open **Gopeed** (desktop) → **Extensions**.
2. Leave the Git URL input box **empty**, then click the **Install** button next to it **5 times quickly in a row** to enable Developer Mode (a folder selector button will appear).
3. Select the folder containing `manifest.json` and `index.js`.

> **Tip:** After updating `manifest.json` (new settings or URL match rules), reload/reinstall the extension folder or restart Gopeed so the changes take effect.

## Extension Settings

Open **Extensions** → click the **Settings (gear)** icon on **Weibo Media Downloader**:

| Setting | Default | Description |
| :--- | :--- | :--- |
| **Preferred Quality** (`quality`) | `Best Available` | Auto-pick `Best Available`, cap at `4K (2160p)` / `1080p` / `720p` / `480p` / `360p`, or list `All Qualities` in Gopeed. |
| **Live Photo Mode** (`livePhoto`) | `Both Image & Video Clip` | Choose `Both Image & Video Clip`, `Image Only`, or `Video Clip Only`. |
| **Weibo Cookie (Optional, for Private / 4K)** (`cookie`) | *(Blank)* | Cookie header or `SUB` value for private, follower-only, VIP, or 4K videos. Leave blank for automatic guest access on public posts. |
| **Custom User-Agent (Optional)** (`userAgent`) | *(Blank)* | Override the built-in Desktop Chrome `User-Agent`. |

## How to Get Your Weibo Cookie (For Private / Follower-Only / 4K Videos)

1. Open [weibo.com](https://weibo.com) in your browser and sign in to your account.
2. Press `F12` to open Developer Tools:
   - **Quick `SUB` cookie:** Go to **Application** (Chrome/Edge) or **Storage** (Firefox) → **Cookies** → `https://weibo.com`, copy the `SUB` value, and paste it directly into the **Weibo Cookie** setting.
   - **Full Cookie header (recommended for strict private posts):** Go to the **Network** tab, refresh the post page, click a request to `weibo.com/ajax/statuses/show`, and copy the full `Cookie` request header value (if `XSRF-TOKEN` is included, the extension automatically sends `X-XSRF-TOKEN` too).

> **Privacy Note:** Your Cookie is stored locally inside Gopeed's extension settings and is **only** sent to `weibo.com` / `weibo.cn` endpoints (never logged and never forwarded to `weibocdn.com` / `sinaimg.cn` CDN hosts or any third-party server).

## Troubleshooting

- **`Login required` / `Cookie has expired`:** Private, friend-circle, or follower-only posts require a fresh logged-in Cookie from an account with permission to view the post.
- **Only `.m3u8` downloaded for a stream:** Live streams or rare videos without progressive `.mp4` files fall back to `.m3u8` HLS playlists.
- **Checking logs:** Gopeed writes extension logs to `logs/extension.log` inside your Gopeed installation/data directory (`DBG` logs are active when installed in Developer Mode).

## License

[MIT](LICENSE)
