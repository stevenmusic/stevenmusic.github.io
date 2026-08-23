# 已定案的版面尺寸（照抄，不要重新發明）

> 這些數字現在散落在四個工具的原始碼裡，是已經在真實裝置上調過的值。新工具需要類似元件（鍵盤、樂譜顯示區、觸控目標）時先照抄這裡，再依實際內容微調——不要憑感覺重新訂一組。完整脈絡（為什麼是這個數字）見各工具原始碼裡的註解，或 `mtk-osmd` skill。

## 鋼琴鍵盤（ScrollScore／HarmonyMap 共用同一份實作）

HarmonyMap 的鍵盤程式碼註解直接寫明「照抄 ScrollScore，連常數與註解都保持原樣」。

| 常數 | 值 | 說明 |
|---|---|---|
| `WHITE_KEY_ASPECT` | `5.2` | 白鍵長寬比目標值 |
| `KB_MIN_H` | `54px` | 鍵盤最小高度 |
| `KB_MAX_H` | `170px` | 鍵盤最大高度（HarmonyMap 額外夾 `window.innerHeight * 0.22`，只有矮螢幕會低於這個上限；直向手機與桌機常見的 844/900px 高度算出來仍是 170px，外觀不變） |
| CSS 首繪高度 | `150px`（`max-width:600px` 降到 `110px`；`max-height:420px` 降到 `64px`） | 只是首次繪製前的 fallback，實際高度由 JS 的 `computeKeyboardHeight()` 覆蓋 |

`computeKeyboardHeight()`：`容器寬度 / 可見白鍵數 * WHITE_KEY_ASPECT`，再夾在 `[KB_MIN_H, KB_MAX_H]`——高度依樂譜/顯示的音域動態算，不是寫死一個數字。

畫布精細度：ScrollScore 用 CSS px（要拿去錄影，尺寸要跟輸出影片一致）；HarmonyMap 額外乘 `devicePixelRatio`（上限 3）讓音名文字在 Retina/iPad 螢幕上銳利。純顯示用途照 HarmonyMap，要輸出影像/影片的照 ScrollScore。

## 觸控目標（三個工具共用同一套規則）

| 情境 | 值 |
|---|---|
| 一般（`pointer:coarse`） | `.btn{height:44px;min-width:44px}` |
| 手機橫向（`max-height:420px`） | `.btn{height:36px}`——44px 會吃掉太多本來就吃緊的高度 |
| 寬度連續縮放 | `min-width:clamp(38px, 10.5vw, 44px)`——320px 機型算出 38px（仍高於 WCAG 2.2 AA 2.5.8 的 24px 下限），400px 以上回到 Apple HIG 建議的 44px |

## 樂譜顯示區（SightScore 的 `.score-frame`）

- 全出血到視窗邊緣：`margin-left: calc(50% - 50vw); margin-right: calc(50% - 50vw);`，搭配 `padding: 56px var(--body-px) 22px`——讓樂譜拿到螢幕實際能給的最大寬度，不受外層 `max-width` 容器限制。
- `--body-px` 三段式：預設（≥900px 或橫向短螢幕）`20px`，600–900px 平板 `20px`，≤600px 手機 `12px`（`.score-frame` padding 相應降到 `48px 8px 12px`）。
- 一般內容容器 `max-width: 64rem`，置中，`padding: 0 var(--body-px) 40px`（這個數字比 `mtk-design-system` 本體建議的 `1100px` 寬一些，是 SightScore 自己的選擇，新工具照本體的 `1100px` 為準，除非有樂譜這種需要更寬顯示區的理由）。

## 版面搜尋與縮放（SightScore，OSMD 樂譜要塞進固定高度時適用）

- `BASE_ZOOM = 1`、`MIN_ZOOM = 0.5`、`SCORE_BOTTOM_GAP = 16`、`MAX_MEASURES_PER_LINE = 5`。
- 縮放地板到了就讓頁面捲動，不要硬擠——平板通常有空間全部塞進一屏，手機常常不夠，兩者用同一套演算法，由 zoom floor 決定結果。

## 影片匯出（ScrollScore）

- `exportFps = 30`（原本 60，因為 Safari 分頁背景時 rAF 被節流才調降）。
- SVG rasterize 用 `4000px` 為單位分塊，避免 iOS canvas 尺寸上限。
- 匯出 canvas 用 `ctx.getContext("2d", {alpha:false})` 減少合成成本；`MediaRecorder` 用 `rec.start(3000)` 每 3 秒切一個 chunk，避免資料不釋放累積記憶體。
