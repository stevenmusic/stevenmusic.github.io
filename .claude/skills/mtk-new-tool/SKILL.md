---
name: mtk-new-tool
description: 從零建立一個新的 Steven Music 網頁工具（單檔 HTML + PWA，部署到 GitHub Pages）。當使用者說要「做一個新工具」「開一個新 repo」「新專案」，或提到 ScrollScore／SightScore／HarmonyMap／LoudMaster 之外的新音樂或實用工具構想時使用。也用於為既有工具補上 PWA、manifest、Service Worker。
---

# 新工具腳手架

## 開工前只問使用者沒講清楚的部分

**預設不要問。** 使用者開口時通常已經把名稱、功能、有沒有相依講完了，下面四題是拿來核對缺什麼，不是問卷：

1. 工具名稱（英文 PascalCase，會直接當 repo 名與網址）＋中文副標
2. 核心功能一句話：使用者**輸入什麼 → 得到什麼**
3. 有沒有需要外部 CDN 函式庫？（OSMD／FFmpeg.wasm／JSZip／Leaflet 等）
4. 需不需要後端？（能純前端就純前端，這是預設）

逐項比對使用者原話：**已經講清楚的直接跳過，只問真的缺的，而且要一次問完**，不要一題一題來回確認。四題都講了就直接開工，不要為了走流程硬問一輪。

## 產出清單

一個新工具 repo 只有這些檔案，不多不少：

```
<ToolName>/
├── index.html              ← 全部程式碼（HTML + CSS + JS 內嵌）
├── manifest.webmanifest
├── sw.js
├── icon-192.png            ← 使用者自行放（先給他規格）
├── icon-512.png
├── icon-1024.png           ← 之後上架 App Store 用
└── README.md
```

以 `templates/index.html` 為起點，不要從空白開始。

## index.html 骨架順序（固定）

1. `<head>`：meta viewport（含 `viewport-fit=cover`）、theme-color `#0C0A07`、description、OG tags、manifest link
2. Google Fonts：Noto Serif TC (700) + Noto Sans TC (400,500)
3. `<style>`：先 `:root` token 區塊（照 `mtk-design-system`），再 layout，再元件
4. `<header>`：返回箭頭 → `https://stevenmusic.github.io/`｜工具名｜EN 切換鈕
5. `<main>`：控制區在上、輸出區在下（手機直向優先）
6. `<footer>`：musicsteven.com 連結
7. `<script>`：`I18N` 物件 → 狀態 → DOM 綁定 → SW 註冊

## 硬性規則

- **不要用 localStorage 以外的儲存**；若在 Claude 的 Artifact 預覽中測試，改用記憶體變數，實際部署版才用 localStorage。
- 所有 CDN 用固定版本號，不要 `@latest`。
- 大檔處理（FFmpeg.wasm、影片匯出）一律先偵測裝置，手機上給明確提示：建議改用電腦。
- 每個新工具建好後，**必須**接著執行 `mtk-hub-seo`，把它加進 hub 首頁與 sitemap。

## 交付格式

一次給完整的 `index.html`、`manifest.webmanifest`、`sw.js`、`README.md` 四個檔案，每個放在獨立的程式碼區塊，開頭標明檔名。使用者會直接用 GitHub 網頁介面貼上建檔。

最後附一行 GitHub Pages 啟用提示：Settings → Pages → Source: `main` / root。
