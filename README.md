# 🤖 每日 AI 熱點速報

每天早上 7:30（台北時間）自動蒐集當日最新 AI 熱點，生成三張資訊圖表，並透過 LINE 推播到手機。

---

## 📸 成果預覽

| 圖① 模型更新 | 圖② 開發者熱榜 | 圖③ 社群熱議 |
|:---:|:---:|:---:|
| ![](2026-04-23/nlm_1_models.png) | ![](2026-04-23/nlm_2_github_tools.png) | ![](2026-04-23/nlm_3_community.png) |

每天一個日期資料夾（`YYYY-MM-DD/`），內含當日全部輸出。

---

## 🔄 自動化流程

```
WebSearch 蒐集今日 AI 熱點
        │
        ▼
 TASK 1  繁體中文報告（6 個段落）
        │
        ├──▶  TASK 2  生成 3 張 HTML 資訊圖（馬克筆風格）
        │
        ├──▶  TASK 3  Claude Preview MCP 截圖 → 3 個 PNG
        │
        ├──▶  TASK 4  NotebookLM MCP 生成資訊圖 → 3 個 PNG
        │
        ├──▶  TASK 5  Push 所有檔案到 GitHub
        │
        └──▶  TASK 6  LINE Messaging API 推播
```

---

## 📂 每日輸出檔案

每次執行後，以下 9 個檔案會存入 `YYYY-MM-DD/` 資料夾並 push 到 GitHub：

| 檔案 | 說明 | 用途 |
|------|------|------|
| `infographic_1_models.html` | 模型發布與更新（橙色主題） | 電腦瀏覽器，1080px |
| `infographic_2_github.html` | GitHub Trending + 新工具（藍色主題） | 電腦瀏覽器，1080px |
| `infographic_3_community.html` | 社群熱議（紅色主題） | 電腦瀏覽器，1080px |
| `screenshot_1_models.png` | HTML 全頁截圖版① | 備用 PNG |
| `screenshot_2_github.png` | HTML 全頁截圖版② | 備用 PNG |
| `screenshot_3_community.png` | HTML 全頁截圖版③ | 備用 PNG |
| `nlm_1_models.png` | NotebookLM sketch_note 資訊圖① | LINE 推播、iPhone 分享 |
| `nlm_2_github_tools.png` | NotebookLM sketch_note 資訊圖② | LINE 推播、iPhone 分享 |
| `nlm_3_community.png` | NotebookLM sketch_note 資訊圖③ | LINE 推播、iPhone 分享 |

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
| **Claude Code** (claude.ai) | 排程觸發器，驅動整個 Agent 工作流 |
| **WebSearch MCP** | 蒐集 TechCrunch、HackerNews、Reddit、GitHub Trending、36kr 等來源 |
| **Claude Preview MCP** | 啟動本機 HTTP server，對 HTML 進行全頁截圖 |
| **NotebookLM MCP** | 以文字報告為素材，生成 sketch_note 風格資訊圖 |
| **GitHub Pages** | 託管 HTML，提供可公開瀏覽的網址 |
| **LINE Messaging API** | 推播文字連結 + 3 張資訊圖到個人 LINE |

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
- **Claude Preview MCP** — Claude Code 已安裝此 MCP Server
- **Git** — 已設定可推送此 repo 的認證憑證
- **LINE Messaging API** — 已建立 Messaging API Channel，Bot 已加為好友

### 本機資料夾結構

```
C:\Users\{user}\Desktop\Claude\
├── ai-daily-report\          ← 每日輸出暫存區（9 個檔案）
├── AI-News\                  ← 本 repo 的本機 clone
│   ├── YYYY-MM-DD\           ← 每日歸檔資料夾
│   └── README.md
└── .claude\
    └── launch.json           ← Claude Preview MCP server 設定
```

---

## 📅 排程設定

| 項目 | 值 |
|------|-----|
| 執行時間 | 每天早上 **7:30**（台北時間，UTC+8） |
| Cron 表達式 | `30 23 * * *`（UTC） |
| 觸發平台 | Claude Code Remote Trigger |

> ⚠️ **注意**：觸發器執行時需要本機電腦處於開機狀態，且 Claude Code 在背景執行中。若早上電腦未開機，當天報告將不會產出。

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
```

**第 2–4 則 — sketch_note 資訊圖片**（直接顯示於 LINE 對話中）

---

## 📰 報告涵蓋範圍

從以下來源蒐集當日最新動態：

| 來源 | 類型 |
|------|------|
| TechCrunch | AI 產業新聞 |
| HackerNews | 技術社群熱議 |
| Reddit (r/MachineLearning, r/LocalLLaMA, r/ChatGPT) | 社群討論 |
| GitHub Trending | 當日最熱門 Repo（含星數、語言） |
| Twitter / X | AI 研究者即時動態 |
| 36kr | 中文科技媒體 |

分三張圖呈現：

1. **圖① 模型更新** — 各大廠模型發布、版本更新、社群反應
2. **圖② 開發者熱榜** — GitHub Trending + 新工具發布與定價
3. **圖③ 社群熱議** — 當日最熱話題排行、熱度指標、明日預告

---

## 🔗 相關連結

- [📖 GitHub Pages 瀏覽](https://shuanwu.github.io/AI-News/)
- [Claude Code](https://claude.ai/code)
- [NotebookLM](https://notebooklm.google.com)
- [LINE Developers Console](https://developers.line.biz)
