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
| 樂譜渲染 | OpenSheetMusicDisplay (OSMD)。ScrollScore 用 CDN；SightScore 為求離線穩定改本機 vendor，兩者版本不同（見 `mtk-osmd`） |
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

### ScrollScore `/ScrollScore/`
捲動樂譜播放器＋鍵盤視覺化＋影片匯出（單檔 `index.html`，OSMD **2.0.0**，CDN 載入）。
- 移調用自訂的 `transposeMusicXML(xmlText, semitones)`：先算五度圈上的 fifths shift，再做字母級數位移改寫 MusicXML——不是碰 OSMD 的 TransposeCalculator，也不是單純加減半音。改移調前先看這支函式。
- 左右手鍵盤配色沒有集中常數，是 `drawKeyboard` 依 `staff`（1=右手金色系、2=左手青色系）直接寫死 hex，attack 態另有一組色碼；改配色要兩組一起改。
- 影片匯出用 `setTimeout` 排程而不是 `requestAnimationFrame`：分頁切到背景／鎖螢幕時 rAF 會被瀏覽器節流到個位數 fps，`exportFps` 已從 60 調降到 30。改 render loop 前一定要先讀原始碼裡對應的註解，不要憑直覺改回 rAF。
- 鋼琴音色是直接 fetch `tonejs.github.io/audio/salamander/` 的取樣檔（`PIANO_BASE`），沒有引入 Tone.js 函式庫本身。

### SightScore `/SightScore/`
ABRSM 視奏題生成——**目前只有 ABRSM，沒有 Trinity**（規則庫 `src/rules/abrsm-piano-grades.json` 只有一份，程式碼裡完全沒有 Trinity 的表或切換邏輯）。多檔案結構（`src/app/`、`src/generator/`），OSMD **2.1.0**，本機 vendor、非 CDN。
- 生題是規則驅動的生成管線（選調 → 拍號 → 小節數 → 和聲骨架 → 節奏 → 音高 → 表情），不是固定題庫也不是 Markov——同一份 `buildStaff()` 跑所有級數，難度差異全部來自 `abrsm-piano-grades.json` 裡的欄位。規則速查表見 `mtk-abrsm-rules`。
- **目前並非完全離線**：沒有 Service Worker（全 repo 沒有 `sw.js`），鋼琴取樣（`tonejs.github.io`）與 Google Fonts 都是執行期才 CDN fetch。生譜本身離線沒問題，但這兩個外部依賴會讓「完全離線」的品質底線（見第 6 節）掛掉，要修。
- OSMD 有多處手動修正（量小節編號只在每行開頭標一次、強制對齊最後一行、tempo 術語置中要事後修正、`RenderXMeasuresPerLineAkaSystem` 只是目標不保證照做）。細節見 `mtk-osmd` skill，改樂譜版面前先讀。

### HarmonyMap `/HarmonyMap/`
互動和弦／音階理論工具（單檔 `index.html`，無外部 JS 函式庫）。
- 音名拼寫是照樂理規則算「字母＋升降記號」（`spell()`），不是 12 音查表——Cdim7 會正確拼成 `C E♭ G♭ B♭♭`，不會變成等音的 `C D♯ F♯ A`。三重升降以上才退回簡易等音拼寫，改拼寫邏輯要保留這個規則。
- 鍵盤渲染（`drawKeyboard`／`computeKeyboardHeight`／`KB_MIN_H=54`／`KB_MAX_H=170`）是照抄 ScrollScore 的實作，連常數都一樣；差異是這裡額外乘 `devicePixelRatio`（上限 3）讓音名文字銳利（ScrollScore 因為要錄影所以維持 CSS px）。尺寸細節見 `mtk-design-system` 的 `references/layouts.md`。
- 沒有五度圈或指板圖，唯一互動圖是 Canvas 鍵盤，不要假設可以直接複用其他圖表元件。

### LoudMaster `/LoudNorm/`（repo 名仍是 LoudNorm，顯示名稱是 LoudMaster，改動任一處不要「順手統一」）
瀏覽器端 -14 LUFS 響度正規化，靠 FFmpeg.wasm 的 `loudnorm`／`ebur128` filter 量測與處理，不是自寫 BS.1770。
- FFmpeg core 已自架在 `vendor/ffmpeg/`（不是 CDN），因為 jsdelivr/unpkg 在 Safari 上有 worker 跨網域問題；版本釘死 `0.12.6`，約 31MB，行動網路下載容易在串流讀取途中失敗，已加重試邏輯。
- 這顆 wasm 沒編譯 libsoxr，只能用預設 swr 重取樣，「限幅後再做有損編碼」時真實峰值會多超出約 1dB，已用 `NO_SOXR_EXTRA_SAFETY=1.1` 補償——改動響度演算法前要知道這個限制還在。
- 手機上真的會因為記憶體不足讓預覽/分享失敗（同時持有兩份完整轉檔結果最容易觸發），改 A/B 比對或匯出流程前先看 `renderAndVerify`／`shareOrDownloadBlob` 附近的註解。

### Hub `stevenmusic.github.io`
首頁，四工具入口。改動見 `mtk-hub-seo`。

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
