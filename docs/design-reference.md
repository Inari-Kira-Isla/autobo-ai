# AutoBo 設計技法參考 — 互動履歷城市站拆解

> 用途：抽取一個高完成度 vibe-coding 網站嘅可複用互動 pattern，作為 AutoBo 網站**日後升級嘅藍本**。呢份係參考文件，唔改 live 站。要落地先揀 pattern、逐個開 PR。

---

## 來源（provenance）

- **出處**：Facebook 群組「Claude Taiwan」貼文（作者 陳彥妃 Jennifer Chen），2026-07-06 讀取。
- **作品網址**：`https://this-person-jennifer.pages.dev/`（「陳彥妃的無名小站」，Cloudflare Pages）。
- **性質**：一個 gamified 互動式個人履歷網站——SimCity 風格城市 hero，點擊建築物跳去唔同 section。
- **作者自述製作流程**（值得記低嘅 SOP，見尾段）：Gemini Pro 生成精細圖像物件 → Claude Design 做全部設計 → Claude Code 實作，全程 Claude Fable 5 + Opus 4.8。花約 10 小時 + 等待，約 1 日多。
- ⚠️ 參考對象係「個人履歷站」，AutoBo 係 B2B concierge 品牌站。**唔可以照搬內容**，只借互動技法 + 資訊節奏，再翻譯返做 AutoBo 自己嘅「自動波 P-R-N-D」語言。

---

## AutoBo 現況（對照基準，2026-07-05 v1）

`index.html` + `services.html` + `style.css`，已上線 `autobo-ai.vercel.app`。**已經有**：

- 自動波 gearbox 品牌隱喻（P–R–N–D、D1/D2/D3 三檔 ladder）
- GSAP + ScrollTrigger：hero 入場淡入、齒輪 rail 隨 scroll 填充、card/stat scroll reveal
- `prefers-reduced-motion` 基礎處理（reduce 時直接顯示、跳過 GSAP）
- 4 格數據 stat（8 模板 / 3 語言 / 0% 亂答 / 100% 成本透明）
- 齊 AEO：Schema.org Organization+Service、FAQPage、Open Graph、llms.txt、canonical

所以下面每個 pattern 都標明【AutoBo 已有 / 部分有 / 缺口】，避免重覆造輪。

---

## 可複用 Pattern × AutoBo 對接

### 1. Gamified 互動 hero（點擊物件導航）【缺口 · 最高價值】
Jennifer 站：城市裏每座建築 = 一個 section 入口（戰情處=戰績、指揮中心=技能、典藏館=歷程、藝文特區=專案、我的家=關於），加懸浮快捷鍵。把「睇履歷」變「逛城市」。

**AutoBo 翻譯**：唔好抄城市，用**自己盤生意嘅場景**——一個可點擊嘅「車房 / 儀表板」或者「檔位控制台」：
- 波棍 P-R-N-D 四檔本身就係現成隱喻：`D` 檔 → 落地流程、`N` 檔 → 服務目錄、`R` 檔 → 案例回顧、`P` 檔 → 關於/聯絡。
- 或者一部車嘅儀表：轉速錶=戰績、油量=成本透明、GPS=悶工診斷入口。
- 點擊某部件 → 平滑 scroll 或彈出對應 section。手機 fallback 用一列大 icon 按鈕（Jennifer 站手機都係退化成 icon list）。

**落地成本**：中。需要一張 hero 主圖（可跟 Jennifer 用 Gemini Pro 出圖）+ image map / 絕對定位熱區 + GSAP 平滑滾動。

### 2. Messenger 式介紹彈窗【缺口 · 同定位天生契合】
Jennifer 站：一個 FB 聊天 UI 風格彈窗，頭像 +「陳彥妃（線上）」+ 一段自我介紹，仲有假嘅「相片/檔案/通訊/遊戲」分頁。

**AutoBo 翻譯**：AutoBo 賣點就係「AI concierge，唔肯定就轉人手」——**用一個聊天氣泡做 hero 入場再自然不過**：
- 開場氣泡：「Hi，我係自動波。想擺脫邊樣悶工？」→ 兩三個 quick-reply chip（客戶查詢 / 報價單 / 對數 / 社群排程）→ 點任一個平滑帶去對應服務 + 落 CTA「5 分鐘診斷」。
- **順便示範產品本身**（chatbot + 唔肯定轉人手），一石二鳥。
- 保持克制：B2B 站唔好用得太花，一個入場氣泡 + 可關閉就夠。

**落地成本**：低-中。純 HTML/CSS/JS，唔使真 backend（scripted 對白即可）。

### 3. 數據戰績牆【部分有 · 可擴充，但守紅線】
Jennifer 站：一整幅「大字數字 + 一句戰果」（390萬流量、2%→32% 辦卡率、破千萬觀看…），視覺衝擊力強。

**AutoBo 翻譯**：現有 4 格可擴成一幅「客戶成果牆」（幫 X 舖每日慳返 N 個鐘、查詢回覆由 M 分鐘 → 秒級…）。
- 🔴 **紅線**：AutoBo 係真品牌，任何客戶數字/成果**必須真**，走 `brand_facts` SSOT，未有真數據**唔准砌假數上牆**。宁可維持而家 4 格「機制型」數字（0% 亂答 / 100% 成本透明——呢啲係承諾唔係戰績，可留）。
- 有真 case 之前，呢格擺住「落地流程數據」（模板數、支援語言、平均落地週期）即可。

### 4. prefers-reduced-motion 深度無障礙【部分有 · 可加深】
Jennifer 站做得比一般深：reduce 時唔止跳過動畫，仲**城市人物定格、停用氣泡與脈動**，畫面靜態但完整。作者特別提呢點係 Fable 5 主動加，佢自己都冇諗過。

**AutoBo 翻譯**：現時 reduce 只係「直接 .in 顯示 + return 唔跑 GSAP」。若日後加咗 gamified hero / 脈動氣泡 / 車輛動畫，記得同步：reduce 時要**明確凍結**每個 loop 動畫（波棍唔郁、氣泡唔脈動、燈唔閃），而唔係靠冇跑 GSAP 就當數。呢個係 AEO/a11y 加分位，AI 引擎同無障礙審查都食。

### 5. 橫向滑動時間軸【缺口 · 中價值】
Jennifer 站：職涯歷程用左右箭嘴橫向滑動的年份卡片，每張可展開「查看完整內容」。

**AutoBo 翻譯**：適合做「一單落地係點行」時間軸（診斷 → 選模板 → 用真資料試 → 上線 → 持續運行 + 轉人手把關），或者未來嘅品牌里程碑。橫向 + 展開卡片省縱向空間，手機體驗好過長列。

### 6. 編輯級資訊節奏【部分有 · 抵學】
Jennifer 站：每個 section 有 `01`–`06` 大編號 + eyebrow 小標，節奏分明。AutoBo 已有 eyebrow（`點樣運作`/`為乜揀自動波`），可再加 section 序號強化「逐檔推進」嘅閱讀感（啱 gearbox 隱喻）。

### 7. 背景音樂 toggle【缺口 · 不建議】
Jennifer 站有 BGM 播放/靜音掣。B2B concierge 站唔建議加自動音樂（觀感 + a11y 負分），記低但列**唔採用**。

---

## 快速優先序（若日後升級）

| Pattern | 價值 | 成本 | 同 AutoBo 契合度 | 建議 |
|---|---|---|---|---|
| 2 · Messenger 介紹氣泡 | 高 | 低 | ★★★（示範產品本身） | **先做** |
| 1 · Gamified P-R-N-D hero | 高 | 中 | ★★★（現成品牌隱喻） | 次做（要出圖） |
| 4 · reduced-motion 加深 | 中 | 低 | ★★（a11y/AEO 加分） | 隨 1/2 一齊做 |
| 5 · 橫向時間軸 | 中 | 中 | ★★ | 有內容先做 |
| 6 · section 編號節奏 | 低 | 極低 | ★★ | 順手加 |
| 3 · 戰績牆擴充 | 中 | 低 | ★（受真數據限制） | 等 brand_facts 有真 case |
| 7 · 背景音樂 | — | — | ✗ | 不採用 |

---

## 附：Jennifer 個 vibe-coding 製作 SOP（可內部沿用 / 教客戶）

1. **圖像先行**：用 **Gemini Pro** 生成精細圖像物件（作者指 Gemini Pro 較易達到想要嘅視覺效果）。
2. **設計定稿**：用 **Claude（Design）** 完成全部視覺 / 版面設計。
3. **實作**：**Claude Code** 落地，模型全程 **Fable 5 + Opus 4.8**。
4. **無障礙由模型主動補**：Fable 5 自動加 `prefers-reduced-motion`，值得每次都保留呢個習慣。
5. 時程參考：一個中小型互動站約 10 小時實作 + 等待，總跨度約 1 日多。

> 呢套「Gemini 出圖 → Claude 設計 → Claude Code 實作」流程，本身就係 AutoBo 可以**打包俾客戶**嘅一條 concierge 服務線（客戶要 landing / 作品站時），亦係做上面任何 pattern 升級時嘅內部 SOP。
