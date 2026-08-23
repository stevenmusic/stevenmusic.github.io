# CLAUDE.md — Steven Music 工具箱專案憲法

> 這份檔案每次開工都會自動載入。只放「永遠成立的鐵律」。
> 需要條件式、流程式的內容 → 寫成 `.claude/skills/*/SKILL.md`，不要塞進這裡。

## 1. 開發環境現實（最重要，違反這條的建議一律無效）

- 開發者 **只有 iPad / iPhone**，沒有本機開發環境、沒有終端機、沒有 Mac。
- 唯一的編輯途徑：**GitHub 網頁介面**（直接 commit 到 `main`）、Safari、WP File Manager。
- 因此：
  - **不要**建議 `npm install`、`git clone`、`build` 步驟，除非該步驟能在雲端（GitHub Actions / Codemagic）完成。
  - **不要**產生需要打包（bundler）的專案結構。
  - 交付一律是**可直接貼上覆蓋的完整檔案**，不要給 diff、不要給「在第 X 行加入」的補丁。
  - 一個工具 = **一個 `index.html` 單檔**，CSS 與 JS 全部內嵌。外部相依一律用 CDN。

## 2. 技術棧（固定，不要提議替換）

| 項目 | 選擇 |
|---|---|
| 語言 | vanilla HTML / CSS / JS，無框架、無 build step |
| 部署 | GitHub Pages，repo 根目錄 `index.html` |
| 樂譜渲染 | OpenSheetMusicDisplay (OSMD) via CDN |
| 音訊 | Web Audio API；重編碼用 FFmpeg.wasm |
| 其他常用 | JSZip、html2canvas、Leaflet.js |
| 後端（必要時） | Node.js / Express on Render |
| PWA | 自寫 `manifest.webmanifest` + `sw.js`，離線可用 |

## 3. 設計系統（不可更動）

```
--bg:      #0C0A07   /* 近黑底 */
--ink:     #F4ECDA   /* 米白字 */
--gold:    #C9A24B   /* 金色主色 */
```

- 標題字體 `Noto Serif TC`，內文字體 `Noto Sans TC`。
- 介面語言：**繁體中文為主**，內建 EN 切換（i18n 物件，不用外部套件）。
- 每個工具的 header 左上必須有**返回 hub 的箭頭**，連到 `https://stevenmusic.github.io/`。
- 細節規範見 skill：`mtk-design-system`。

## 4. 現有工具（改動時要保持一致）

| 工具 | 路徑 | 用途 |
|---|---|---|
| ScrollScore | `/ScrollScore/` | 捲動樂譜播放器＋鍵盤視覺化＋影片匯出 |
| SightScore | `/SightScore/` | ABRSM/Trinity 級數視奏題生成 |
| HarmonyMap | `/HarmonyMap/` | 和弦／音階互動理論工具 |
| LoudMaster | `/LoudNorm/` | 瀏覽器端 -14 LUFS 響度正規化（repo 名仍是 LoudNorm） |
| Hub | `stevenmusic.github.io` | 首頁，四工具入口 |

**注意**：LoudMaster 的顯示名稱與 repo 名稱不同，改動時不要「順手統一」。

## 5. 溝通規則

- 回答**簡短、直接、以實作為主**。不需要前言、不需要總結我剛剛做了什麼。
- 不確定的地方**先問一句**，不要自行假設後寫出整份錯的檔案。
- 檔案交付：完整檔案 + 一句「改了什麼」即可。

## 6. 品質底線（每個工具都要通過）

- [ ] iPhone 直向可用，無橫向捲動
- [ ] 鍵盤可操作，focus 可見
- [ ] `prefers-reduced-motion` 有處理
- [ ] 離線可開（Service Worker 已快取殼層）
- [ ] `<html lang>`、`meta description`、OG 圖齊全
- [ ] 無 console error
