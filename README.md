# 🤖 每日 AI 熱點速報

每天早上 7:30（台北時間）自動蒐集當日最新 AI 熱點，生成三張資訊圖表，並透過 LINE 推播到手機。

---

## 📸 成果預覽

| 圖① 模型更新 | 圖② 開發者熱榜 | 圖③ 社群熱議 |
|:---:|:---:|:---:|
| ![](2026-04-27/nlm_1_models.png) | ![](2026-04-27/nlm_2_github_tools.png) | ![](2026-04-27/nlm_3_community.png) |

每天一個日期資料夾（`YYYY-MM-DD/`），內含當日全部輸出。

---

## 🔄 自動化流程

```
[07:30] Windows Task Scheduler 觸發本機 claude -p
              │
              ▼
        §1  Haiku Agent — 並行 WebSearch × 6
            蒐集模型發布 / GitHub Trending / 社群熱議
            → news_data_YYYY-MM-DD.json
              │
              ▼
        §2  Python fill_template.py — 零 LLM token
            JSON → HTML 模板填充
            → infographic_{1,2,3}_*.html × 3
              │
              ▼
        §3  NotebookLM MCP（單一 notebook，3 sources）
            studio_create × 3（馬克筆手繪風 infographic）
            + Python httpx 下載 PNG
            → nlm_{1,2,3}_*.png × 3
              │
              ▼
        §4  git push → ShuanWu/AI-News
              │
              ├──▶  GitHub Actions — Playwright 截圖
              │                    → screenshot_*.png
              │
              └──▶  LINE Messaging API 推播
                    （文字連結 + 3 張 NotebookLM 圖）
```

---

## 📂 每日輸出檔案

每次執行後，以下檔案會存入 `YYYY-MM-DD/` 資料夾：

| 檔案 | 說明 | 用途 |
|------|------|------|
| `infographic_1_models.html` | 模型發布與更新（橙色主題） | 電腦瀏覽器，1080px |
| `infographic_2_github.html` | GitHub Trending + 新工具（藍色主題） | 電腦瀏覽器，1080px |
| `infographic_3_community.html` | 社群熱議（紅色主題） | 電腦瀏覽器，1080px |
| `nlm_1_models.png` | NotebookLM 馬克筆手繪資訊圖① | LINE 推播、iPhone 分享 |
| `nlm_2_github_tools.png` | NotebookLM 馬克筆手繪資訊圖② | LINE 推播、iPhone 分享 |
| `nlm_3_community.png` | NotebookLM 馬克筆手繪資訊圖③ | LINE 推播、iPhone 分享 |
| `screenshot_1_models.png` | HTML 全頁截圖（GitHub Actions 自動生成） | 備用 PNG |
| `screenshot_2_github.png` | HTML 全頁截圖（GitHub Actions 自動生成） | 備用 PNG |
| `screenshot_3_community.png` | HTML 全頁截圖（GitHub Actions 自動生成） | 備用 PNG |
| `line_tagline.txt` | 今日亮點摘要（3 句）| LINE 訊息末行 |

---

## 🌐 線上瀏覽

GitHub Pages 網址格式：

```
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_1_models.html
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_2_github.html
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_3_community.html
```

---

## 🛠 技術架構

| 元件 | 功能 |
|------|------|
| **Windows 工作排程器** | 每天 07:30 觸發本機 `claude -p` 執行完整 pipeline |
| **Claude Code CLI** | 驅動所有 Agent 工作流，載入本機 MCP 工具 |
| **Haiku Agent** | 以 1/20 的 token 成本執行 WebSearch 並輸出結構化 JSON |
| **Python fill_template.py** | 零 LLM token 將 JSON 填入 HTML 模板 |
| **NotebookLM MCP** | 以結構化文字為素材，生成馬克筆手繪風格資訊圖 PNG |
| **GitHub Pages** | 託管 HTML，提供可公開瀏覽的網址 |
| **GitHub Actions** | push 後自動 Playwright 截圖 + LINE 推播 |
| **LINE Messaging API** | 推播文字連結 + 3 張 NotebookLM 資訊圖到個人 LINE |

### HTML 設計規格（馬克筆風格）

```css
/* 色彩系統 */
--cream:  #FEFDF5;   /* 背景 */
--ink:    #1C1914;   /* 主文字 */
--orange: #E55A00;   /* 圖① 強調色 */
--blue:   #1D4ED8;   /* 圖② 強調色 */
--red:    #C42020;   /* 圖③ 強調色 */

/* 手繪卡片效果 */
border: 2px solid #1C1914;
border-radius: 3px 8px 5px 4px / 5px 3px 8px 4px;   /* 不對稱邊角 */
box-shadow: 3px 3px 0 #1C1914;
transform: rotate(-0.35deg);   /* 奇數卡片 */
transform: rotate(0.25deg);    /* 偶數卡片 */

/* 螢光筆標記 */
background: linear-gradient(transparent 38%, rgba(253,224,0,0.42) 38%);
```

字型：`Caveat`（手寫標題）＋ `Noto Sans TC`（中文內文）＋ `Space Grotesk`（英文數字）

---

## ⚙️ 環境需求

- **Claude Code** — 安裝於本機，登入 claude.ai 帳號
- **NotebookLM MCP** — 已安裝 `nlm` CLI 並完成 `nlm login` Google 帳號認證
- **Python 3.12** — 用於 `fill_template.py` 零 token HTML 生成
- **Git** — 已設定可推送此 repo 的認證憑證
- **LINE Messaging API** — 已建立 Messaging API Channel，Bot 已加為好友

### 本機資料夾結構

```
C:\Users\{user}\Desktop\Claude\
├── ai-daily-report\                ← 每日輸出暫存區
│   ├── news_data_YYYY-MM-DD.json   ← Haiku Agent 輸出
│   ├── infographic_{1,2,3}_*.html  ← Python 填模板輸出
│   ├── nlm_{1,2,3}_*_YYYY-MM-DD.png ← NotebookLM 下載
│   ├── logs\                       ← 每日執行 log（Task Scheduler）
│   │   └── YYYY-MM-DD.log
│   ├── run_daily_report.ps1        ← Task Scheduler 排程腳本
│   ├── fill_template.py            ← HTML 模板填充腳本
│   └── templates\                  ← HTML 模板
└── AI-News\                        ← 本 repo 的本機 clone
    └── YYYY-MM-DD\                 ← 每日歸檔資料夾
```

---

## 📅 排程設定

| 項目 | 值 |
|------|-----|
| 執行時間 | 每天早上 **7:30**（台北時間，UTC+8） |
| 觸發平台 | Windows 工作排程器（Task Scheduler） |
| 任務名稱 | `AI-Daily-Report` |
| 執行指令 | `pwsh.exe -NonInteractive -ExecutionPolicy Bypass -File run_daily_report.ps1` |
| 若錯過（電腦關著） | 開機後自動補跑（StartWhenAvailable） |
| 執行上限 | 1 小時 |
| Log 位置 | `ai-daily-report\logs\YYYY-MM-DD.log` |

> ⚠️ **注意**：排程在本機執行，電腦需處於開機（且已登入）狀態。若早上電腦未開機，當天報告將在下次開機時補跑。GitHub Actions 可作為備援產生截圖，但 NotebookLM 資訊圖仍需本機執行。

---

## 📱 LINE 推播格式

每日收到 4 則訊息：

**第 1 則 — 文字連結**
```
每日AI熱點速報 YYYY-MM-DD

圖① 模型更新:
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_1_models.html

圖② 開發者熱榜:
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_2_github.html

圖③ 社群熱議:
https://shuanwu.github.io/AI-News/YYYY-MM-DD/infographic_3_community.html

今日亮點：{line_tagline.txt 內容}！
```

**第 2–4 則 — 馬克筆手繪資訊圖**（直接顯示於 LINE 對話中）

---

## 📰 資料蒐集邏輯

### 時效性

- **新聞 / 模型 / 工具 / 社群**：只納入過去 **24 小時**內發布的項目（搜尋時帶 `after:YYYY-MM-DD` 過濾）
- 若某段落不足 3 筆，自動退到 48 小時窗口並標注 fallback
- **GitHub Trending**：使用週榜（本週一至發報當下），與其他段落的時間窗口不同

### 來源

| 來源 | 類型 | 時間窗口 |
|------|------|----------|
| openai.com / anthropic.com / deepmind.google | 模型官方發布 | 24h |
| HackerNews | 技術社群熱議 | 24h |
| Reddit (r/MachineLearning, r/LocalLLaMA) | 社群討論 | 24h |
| 36kr / blocktempo | 中文科技媒體 | 24h |
| github.com/trending?since=weekly | GitHub 週榜（真實星數成長） | 本週一起 |

### 分三張圖呈現

1. **圖① 模型更新**（橙色）— 各大廠模型發布、版本更新、定價、社群反應
2. **圖② 開發者熱榜**（藍色）— GitHub 週榜真實漲星數 × AI Top 5 + 非 AI Top 3 + 焦點解讀
3. **圖③ 社群熱議**（紅色）— 當日最熱話題排行、熱度指標（0–100）、明日關注預告

---

## 🔗 相關連結

- [📖 GitHub Pages 瀏覽](https://shuanwu.github.io/AI-News/)
- [Claude Code](https://claude.ai/code)
- [NotebookLM](https://notebooklm.google.com)
- [LINE Developers Console](https://developers.line.biz)
