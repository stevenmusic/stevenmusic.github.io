---
name: mtk-osmd
description: OpenSheetMusicDisplay（OSMD）在 ScrollScore／SightScore 上踩過的雷與對應修法——自動換行、量小節編號、tempo 術語定位、多小節休止合併、讀取渲染後的 SVG 幾何、載入方式、移調。當要修改任一個用 OSMD 的工具的樂譜渲染、版面、量小節、cursor、休止符合併，或要在新工具導入 OSMD 時使用。
---

# OSMD 踩雷筆記（ScrollScore + SightScore 共用）

兩個工具各自獨立踩過這些雷，**不要在第三個工具重新踩一次**。

## 版本與載入方式不一致（先注意）

- ScrollScore：OSMD **2.0.0**，CDN 載入（`cdn.jsdelivr.net`）。
- SightScore：OSMD **2.1.0**，本機 vendor（`vendor/opensheetmusicdisplay.min.js`），理由是「不需要 build step 或 npm install 就能跑」——連 CDN 都不要，離線更穩。
- 新工具若要完全離線（App Store 4.2 需求，見 `mtk-ship-appstore`），照 SightScore 的做法 vendor 一份，不要用 CDN。

## 版面／自動換行

- `RenderXMeasuresPerLineAkaSystem` **只是目標，不保證真的照做**：容器裝不下時 OSMD 會自己再拆行，拆法還可能跟預期不一樣（例如要求「每行 3 小節」的 6 小節曲子，可能拆成「每行 2 小節」共三行，而不是「每行 3 小節」共兩行）。要驗證真的排對，得自己寫檢查函式比對每行實際小節數（SightScore 的 `matchesRequestedLayout()`）。
- OSMD 預設的自動換行是貪婪演算法（能塞就塞），內容除不盡時最後一行常常只剩一兩小節孤伶伶。
- `stretchLastSystemLine: true` 可以強制最後一行對齊寬度，否則兩行小節數一樣、結尾位置卻對不齊。
- `FixedMeasureWidth = true` 是讓「每行 N 小節」這件事在計算版面時有意義的前提，拿掉版面搜尋會整個失準。

## 量小節編號

- OSMD 預設每行開頭都編號，還會**額外**照固定間隔編號一次，跟換行位置對不上時編號會跑到行中間。改成 `drawMeasureNumbersOnlyAtSystemStart: true` 讓每行只在開頭標一次，才是正常樂譜的編號方式。

## Tempo／術語文字定位

- OSMD 把 tempo 術語置中在「第一小節的起始事件」上，如果第一小節是整小節休止（常見於初階視奏題，左右手交替），術語就會飄到休止符中間，而不是曲子開頭。目前的修法是事後掃 SVG 找「唯一的粗體非斜體文字」節點，用時間記號前的空隙手動重新定位（見 SightScore `pinTempoTermPosition()`）。

## 多小節休止

- 不要依賴 OSMD 的 `AutoGenerateMultipleRestMeasuresFromRestMeasures` 自動合併多小節休止，只在來源 MusicXML 已經標記的情況下才有效。ScrollScore 是自己寫 MusicXML 前處理（`splitRestMeasures`/`mergeRestMeasures`）來控制，不靠 OSMD。

## 讀取渲染結果的幾何位置（捲動、高亮、playhead）

- 不要透過 OSMD 自己的 graphic model 去對位，直接讀渲染出來的 SVG 更穩：每個小節是 `g.vf-measure`（id 就是小節編號），音符是 `g.vf-stavenote`，譜線是 `g.staffline`。這些是底層 VexFlow 畫出來的 class，不是 OSMD 的公開 API，但實測比較可靠（ScrollScore、SightScore 都是這樣做）。
- 每個系統（system）第一小節前面畫的譜號/調號/拍號家具，在算「內容起始位置」時要排除，不然對位會整個偏移。

## 容器尺寸相關

- 容器一定要維持非零寬度：`render()` 時 OSMD 會量測容器，`display:none` 或零寬度的父層會讓譜表往反方向（負 x）排版。要隱藏用 `visibility:hidden` 而不是 `display:none`。
- 匯出成圖片/影片時，SVG 的 `preserveAspectRatio` 一定要明確設成 `"none"`，否則 width/height 屬性比例跟 viewBox 對不上時（OSMD 某些樂譜會有零頭差），疊加的高亮層會跟樂譜整張橫向偏移幾 px。
- rasterize 大張樂譜時用固定 tile（如 4000px）分塊畫，避免 iOS 的 canvas 尺寸上限。

## 移調

- 不要直接呼叫 OSMD 的 transpose API 當預設路線。ScrollScore 是自己重寫 MusicXML（五度圈算 fifths shift → 字母級數位移 → 重算升降記號），樂理上更可控，尤其是保留「這個音是不是被升過的導音」這種訊息。若要改回用 OSMD 內建的 transpose，先確認它是否也保留這個語意。
