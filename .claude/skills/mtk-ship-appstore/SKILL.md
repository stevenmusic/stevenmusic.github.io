---
name: mtk-ship-appstore
description: 把既有的單檔 HTML 工具包裝成 iOS App 並送審 App Store，在沒有 Mac 的情況下用 Capacitor + 雲端建置完成。當使用者提到「上架」「App Store」「打包成 app」「Capacitor」「TestFlight」「送審被拒」時使用。也用於準備 App Store Connect 所需的圖示、截圖、隱私權宣告與審核回覆。
---

# HTML 工具 → App Store（無 Mac 路線）

## 先講清楚三個現實

1. **iOS 建置一定要 macOS + Xcode。** 沒有 Mac 就必須用雲端建置服務（Codemagic、Ionic Appflow、Expo EAS 皆可）。GitHub Actions 也有 macOS runner，可自寫 workflow——這是唯一「全部在 GitHub 網頁介面完成」的路線。
2. **Apple Developer Program 每年 US$99**，必須先付費才有 App Store Connect。App Store Connect 本身在 iPad Safari 上可以操作（上傳建置檔除外，由雲端 CI 用 API key 直接上傳）。
3. **Apple 審核指南 4.2「最低功能」會擋純網頁殼。** 把網站塞進 WebView 就送審，被拒的機率很高。這是整件事最大的風險，要在動工前處理，不是被拒之後才處理。

## 4.2 的實際解法（選至少兩項）

- **離線可用**：把 HTML/JS/CSS 全部打包進 app bundle，斷網也能用（不是連到 github.io）。
- **原生能力**：檔案匯入匯出（Capacitor Filesystem / Share）、本機通知（練習提醒）、Haptics、背景音訊。
- **真正的原生 UI 外框**：原生分頁列、原生設定頁，不是只有一片 WebView。
- **裝置專屬功能**：麥克風輸入（調音／音準偵測）、Apple Pencil、iPad 分割視窗、外接 MIDI。

ScrollScore 與 SightScore 最容易過關（離線題庫 + 匯出 + 練習提醒）；LoudMaster 因為 FFmpeg.wasm 在 iOS WebView 上效能與記憶體都吃緊，**不建議**作為第一個上架標的。

## 建議的第一個上架目標

以 **SightScore** 為首發：完全離線可生成、無大型相依、體積小、對「這不只是網站」的說明最好寫。

## 流程

1. **準備 repo**：在工具 repo 新增 `capacitor.config.json`、`package.json`，把 `index.html` 等靜態檔放進 `www/`。原本的 GitHub Pages 版本保留不動（Pages 指向根目錄，app 版讀 `www/`）。
2. **加原生能力**：至少接一個 Capacitor 官方 plugin，並在 UI 上讓使用者看得到差別。
3. **設定雲端建置**：GitHub Actions + `macos-latest` runner，步驟為 `npm ci` → `npx cap sync ios` → `xcodebuild archive` → `xcrun altool/notarytool` 上傳 TestFlight。憑證與 App Store Connect API key 放 GitHub Secrets。
4. **App Store Connect**：建立 App ID、填 Bundle ID、上傳圖示與截圖、填隱私權「不收集資料」（若確實不收集）、填年齡分級。
5. **TestFlight 自測**，再送審。

需要實際檔案時，直接產出完整的 `capacitor.config.json`、`package.json`、`.github/workflows/ios.yml`，使用者會用 GitHub 網頁介面貼上。

## 素材規格

- App icon：`1024×1024` PNG，**不可有透明度、不可有圓角**（Apple 自己切）。黑底 `#0C0A07` + 金色符號。
- 截圖：6.9" iPhone（1320×2868）與 13" iPad（2064×2752）各至少一張。
- 隱私權政策網址：必填。放在 `musicsteven.com/privacy`。

## 被拒時

先讀 Resolution Center 的**條款編號**再回應。4.2 → 補原生功能後重送並在回覆中列出具體差異；2.1 資訊不足 → 附示範影片。不要只在回覆文字裡辯解而不改 app。
