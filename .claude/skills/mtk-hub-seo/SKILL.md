---
name: mtk-hub-seo
description: 把新工具登錄到 stevenmusic.github.io hub 首頁，並處理 SEO——meta 標籤、OG 預覽圖、sitemap、結構化資料、中英雙語頁面。當使用者完成一個新工具、要修改 hub 首頁、問「怎麼被搜尋到」「OG 圖」「sitemap」「網站描述」時使用。
---

# Hub 註冊與 SEO

## 新工具上線後的必做清單

1. **hub 首頁 `index.html`**：在「四個工具」區塊新增卡片（標題／副標／一句話說明／三點特色／「適合：」／CTA 連結）。同時更新中英兩份 i18n 字串與頁尾連結列表。
2. **預覽圖**：`preview/<toolname>-zh.webp`（與 `-en.webp`），寬 1200px 以上。alt 文字要描述**畫面上實際看到什麼**，不是工具名稱重複一次。
3. **工具本身的 head**：
   - `<title>ToolName ｜一句話用途 — Steven Music</title>`
   - `meta description` 120–155 字元，寫使用者得到什麼
   - OG：`og:title` / `og:description` / `og:image`（絕對網址）/ `og:url` / `og:type=website`
   - `<link rel="canonical">`
4. **`sitemap.xml`**（hub repo 根目錄）新增一筆 `<url>`，更新 `<lastmod>`。
5. **`robots.txt`** 指向 sitemap。
6. hub 首頁頁尾的「最後更新」日期同步更新。

## 結構化資料

每個工具頁加一段 `SoftwareApplication` JSON-LD：`name`、`applicationCategory: "MultimediaApplication"`、`operatingSystem: "Any"`、`offers.price: "0"`、`inLanguage: ["zh-Hant","en"]`。

## 命名一致性警告

hub 上顯示的是 **LoudMaster**，但 repo 路徑是 `/LoudNorm/`。改動任一處時**不要自作主張統一**，先問。

## 文案風格

hub 卡片文案照現有語氣：短句、講清楚給誰用、不用行銷形容詞。中文用全形標點，工具名與英文保持半形並前後留空格。
