---
name: mtk-design-system
description: Steven Music 黑金設計系統的完整規範——色票、字體、間距、元件樣式、深淺色與無障礙規則。當要新增或修改任何 Steven Music 工具（ScrollScore、SightScore、HarmonyMap、LoudMaster、hub 首頁）的介面、CSS、版面、按鈕、表單、色彩時使用。也用於檢查既有頁面是否偏離設計系統。
---

# Steven Music 黑金設計系統

完整 token 檔在 `references/tokens.css`——需要寫 CSS 時直接讀取並貼入 `<style>` 最上方，不要重打。

## 核心三色（絕對不改）

| 用途 | 值 |
|---|---|
| 底色 | `#0C0A07` |
| 文字 | `#F4ECDA` |
| 主色／強調 | `#C9A24B` |

衍生色一律由這三色推導（見 tokens.css），不要引入第四個色相。唯一例外：狀態色（成功／警告／錯誤）可用低飽和的綠／琥珀／磚紅，亮度須與金色相當。

## 字體

- 標題：`"Noto Serif TC", serif`，700
- 內文／UI：`"Noto Sans TC", sans-serif`，400 / 500
- 數字與參數（LUFS、BPM、級數）：`font-variant-numeric: tabular-nums`

型階：`clamp()` 為主，手機不另寫 media query。

## 版面原則

- 內容最大寬 `1100px`，居中，左右 padding `clamp(16px, 4vw, 32px)`。
- **手機直向優先**：控制區在上，輸出／畫布在下。桌機才並排。
- 圓角統一 `10px`；卡片邊框 `1px solid rgba(201,162,75,.22)`，不用陰影堆疊。
- 金色是**重點色不是背景色**。大面積金底只出現在單一主要行動按鈕。

## 元件慣例

- 主按鈕：金底黑字。次要按鈕：透明底、金色邊框與文字。
- 輸入框／下拉：底色 `#15120D`，金色細邊框，focus 時邊框加亮 + 2px outline。
- 每頁 header 左上固定一個返回箭頭（`←`）連回 `https://stevenmusic.github.io/`，尺寸至少 44×44 觸控區。
- Toast／提示訊息：直接說明發生什麼和怎麼修，不用「抱歉」開頭。

## 無障礙底線

- 文字對比 ≥ 4.5:1（`#F4ECDA` on `#0C0A07` 已達標；金色文字只用於 18px 以上或粗體）。
- `:focus-visible` 一律可見，不可 `outline: none` 了事。
- `@media (prefers-reduced-motion: reduce)` 關閉所有 transition 與捲動動畫。
- 所有圖示按鈕要有 `aria-label`，中英雙語都要。

## i18n

用單一 `I18N = { zh: {...}, en: {...} }` 物件 + `data-i18n` 屬性，切換時遍歷 DOM。語言存 `localStorage`，預設 `zh`。不要引入 i18n 套件。
