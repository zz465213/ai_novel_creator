---
name: ai-novel-creator
description: 小說創作工作流系統（Novel-OS）。當使用者提到「寫小說」、「開始新故事」、「管理稿件」、「規劃大綱」、「繼續我的小說」或任何小說創作相關任務時，請立即使用此 skill。此系統透過三層上下文結構（寫作標準 → 小說專案 → 稿件管理）將 AI 從普通助手轉變為理解作者風格與故事結構的創作夥伴。
---

# AI Novel Creator（Novel-OS Agent Skill）

這是一套結構化的小說創作工作流，核心依賴三層上下文結構，由淺入深為 AI 建立創作所需的完整背景。

## 三層上下文架構

| 層級 | 名稱 | 定義 | 路徑 |
|------|------|------|------|
| 第一層 | 寫作標準（Standards） | 定義「如何寫」：敘事聲音、POV、散文風格、類型指南 | `.novel-os/standards/` |
| 第二層 | 小說專案（Novel） | 定義「寫什麼」＋「接下來寫什麼」：前提、計畫、大綱、角色、任務 | `.novel-os/novels/[編號-故事名稱]/` |
| 第三層 | 章節正文（Chapters） | 實際產出的小說內文 | `.novel-os/novels/[編號-故事名稱]/chapters/` |

## 四大核心指令

使用者說出以下任何一個指令，請載入對應的 sub-skill：

| 使用者指令 | Sub-Skill | 觸發時機 |
|-----------|-----------|---------|
| `/plan-novel` | `plan-novel/SKILL.md` | 開始全新小說專案 |
| `/analyze-manuscript` | `analyze-manuscript/SKILL.md` | 導入現有稿件 |
| `/create-outline` | `create-outline/SKILL.md` | 規劃故事大綱 |
| `/write-scenes` | `write-scenes/SKILL.md` | 執行場景撰寫 |

## 標準工作流程

```
/plan-novel → /create-outline → /write-scenes
                                      ↑
                        （循環直到小說完成）
```

若為既有稿件遷移：
```
/analyze-manuscript → /create-outline（補充大綱）→ /write-scenes
```

## 檔案系統結構

執行各指令後，系統會自動建立以下目錄結構：

```
.novel-os/
├── standards/                        ← 跨所有小說共用的寫作標準
│   ├── writing-style.md              ← 敘事聲音、POV、整體散文風格
│   ├── narrative-techniques.md       ← 故事結構、角色發展、節奏控制
│   ├── writing-techniques.md         ← 通用技術：對話處理、動作戲、字數調整
│   ├── genre-guides/                 ← 依類型分檔，按需載入
│   │   ├── fantasy-sci-fi.md         ← 世界觀設定呈現、科幻邏輯解釋
│   │   ├── literary-fiction.md       ← 角色驅動、內心獨白、精鍊文字質感
│   │   └── mystery-thriller.md       ← 線索佈置、懸念生成、緊張感營造
│   └── writing-style/                ← 細部描寫的延伸設定
│       └── description-style.md      ← 感官細節比例、隱喻偏好、環境描繪節奏
│
└── novels/                           ← 所有小說專案的根目錄
    ├── 001-the-last-bookshop/        ← 第一部小說（完全自包含）
    │   ├── premise.md                ← 故事核心與完整願景
    │   ├── premise-lite.md           ← AI 上下文摘要（精簡版）
    │   ├── writing-style.md          ← 此部小說的風格設定
    │   ├── writing-plan.md           ← 寫作計畫與里程碑
    │   ├── decisions.md              ← 創意決策動態紀錄
    │   ├── story-outline.md          ← 完整故事大綱
    │   ├── story-outline-lite.md     ← 大綱 AI 摘要版
    │   ├── tasks.md                  ← 逐場戲任務清單
    │   ├── sub-specs/
    │   │   ├── character-profiles.md
    │   │   └── world-building.md
    │   └── chapters/                 ← 此部小說的章節正文
    │       ├── chapter-01.md
    │       ├── chapter-02.md
    │       └── ...
    │
    └── 002-another-story/            ← 第二部小說，結構完全相同
        ├── premise.md
        └── ...
```

### 命名規則

| 項目 | 規則 | 範例 |
|------|------|------|
| 小說目錄 | `[三位數編號]-[kebab-case英文名]` | `001-the-last-bookshop` |
| 章節檔案 | `chapter-[兩位數編號].md` | `chapter-01.md` |
| 草稿檔案 | `chapter-[編號]-draft.md` | `chapter-03-draft.md` |

## ⚠️ 語言規定

**所有小說正文一律使用繁體中文撰寫。** 這包含章節正文、角色對話、內心獨白與場景描寫。檔案名稱、路徑與技術性欄位維持英文。若使用者以其他語言請求撰寫小說內文，請自動切換為繁體中文輸出。

---

## 給 AI 的協作原則

- **始終參照三層結構**：撰寫任何內容前，先確認相關層級的設定檔案已讀取
- **風格一致性優先**：每次輸出都應像作者本人的聲音
- **主動追蹤進度**：完成任務後自動更新 `tasks.md` 與 `writing-plan.md`
- **記錄創意決策**：重要的劇情或角色決定應寫入 `decisions.md`

## Standards 載入規則

`standards/` 下的檔案按需載入，不必每次全部讀取：

| 檔案 | 何時載入 |
|------|---------|
| `writing-style.md` | 每次撰寫場景都載入 |
| `narrative-techniques.md` | 規劃大綱、處理節奏問題時載入 |
| `writing-techniques.md` | 處理對話、動作戲、字數調整時載入 |
| `writing-style/description-style.md` | 撰寫大量描寫段落時載入 |
| `genre-guides/literary-fiction.md` | 小說類型為文學小說時載入 |
| `genre-guides/mystery-thriller.md` | 小說類型為推理／驚悚時載入 |
| `genre-guides/fantasy-sci-fi.md` | 小說類型為奇幻／科幻時載入 |

> 類型判斷依據：讀取 `[小說目錄]/writing-style.md` 中的類型欄位，若未設定則參照 `premise.md`。
