---
name: mtk-abrsm-rules
description: ABRSM（以及未來 Trinity）鋼琴視奏各級數出題規則速查——拍號、小節數、調性、音域、節奏、生成提示。當要修改 SightScore 的出題規則、討論某個級數該有什麼難度、或要在新工具沿用同一套規則庫時使用。完整資料的正本在 SightScore repo 的 `src/rules/abrsm-piano-grades.json`，這裡只放濃縮速查表，見 `references/grades.md`。
---

# ABRSM 視奏規則

正本是 SightScore repo 的 `src/rules/abrsm-piano-grades.json`（完整 8 個級數，每級都標了資料可信度 `confidence`）。`references/grades.md` 是**濃縮速查表**，方便討論規則時不用先開那支 1600 多行的 JSON——但改規則時還是要回 SightScore repo 改正本，這裡的表格要記得手動同步，不會自動跟著變。

## 目前只有 ABRSM 鋼琴一份

**Trinity 完全還沒做**——SightScore 的 `src/` 裡沒有任何 Trinity 規則表或切換邏輯，只有文件（`docs/`）裡提過構想。不要假設 Trinity 規則已經存在，也不要為了回答問題憑空生一份數字；真的要做，比照 ABRSM 的 JSON 結構另開一份新表。

## 生成管線是即時算的，不是題庫

SightScore 不是從一堆預先做好的譜挑一張，是每次即時生成 MusicXML 再交給 OSMD 畫。所有級數共用同一份生成函式，難度差異全部來自規則表欄位，不是另外寫程式碼分支——改難度先改規則表，不要去改生成邏輯本身。細節見 `references/grades.md` 最後一節。
