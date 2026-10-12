# AIGC 導演五線提示詞驗收

**aigc-director-five-lines** — 一套給 AI 影像／影片提示詞用的導演前置驗收流程。

先確認**導演意圖**與**五條線**，後生成提示詞。拒絕用「電影感、史詩感、手持、8K」等空泛風格詞取代敘事決策。

> **硬性規則：** 嚴格模式下，只要導演意圖或任一條線為「缺失」，不得輸出最終可生成提示詞。必須先補問缺口，或把導演提出的明確授權判斷轉寫為可確認的暫定假設。

---

## 為什麼需要它

多數 AIGC 提示詞的失敗不是「模型不好」，而是**分鏡上游沒有決策**：

- 畫面好看，但沒有情緒——因為沒指定觀眾該先看懂什麼。
- 角色一直在動，卻不緊張——因為動作沒有起因與結果，剪點也沒對齊峰值。
- 手持很晃，卻沒有現場感——因為每個晃動都沒有鏡內事件作為理由。
- 伏筆埋了，卻毫無作用——因為「藏起來」被寫成「看不見」，後續無法回收。
- 鏡頭接不上——因為沒有入點、出點與承接條件。

本技能把這些問題全部拉回**可驗收的欄位**：缺哪一項就問哪一項，通過了才產出提示詞。

---

## 兩階段架構

| 階段 | 名稱 | 產出 | 結束於 |
|---|---|---|---|
| **A** | 導演提案 | 五線完整度驗收表 ＋ 標準分鏡 | 剪點、動作路徑、參考選擇與節奏規劃 |
| **B** | Seedance 提示詞總監 | 可直接送出的完整 Seedance 提示詞 ＋ 送交參數 ＋ 附件對應 | 可送出的完整提示詞 |

**階段 B 只轉譯，不創作。** 不得新增角色、場景、情節、台詞、道具，也不得改動已核准的動作、機位、鏡頭路徑、節奏與剪點；分鏡若有缺漏，一律退回階段 A 補齊。

---

## 五條線＋一條補充線

| 線 | 回答的問題 | 判定口訣 |
|---|---|---|
| 0. 導演意圖 | 最想讓觀眾感受到什麼？ | 先問感受，不要先問鏡頭、顏色或模型 |
| 1. 動作線 | 事情怎麼發生？ | 前一個動作，必須影響下一個結果 |
| 2. 情緒線 | 人物如何感受？ | 不要演情緒；要演情緒造成的身體變化 |
| 3. 攝影線 | 攝影機如何參與現場？ | 從哪裡拍、朝哪裡拍、角色往哪裡動、鏡頭怎麼動、為何動、在哪裡切出 |
| 4. 視覺線 | 觀眾此刻看哪裡？ | 什麼都清楚，往往就是什麼都不重要 |
| 5. 剪輯線 | 觀眾何時看下一個？ | 動作結束，不等於要等鏡頭結束 |
| 6.（補充）資訊埋藏線 | 伏筆怎麼藏、怎麼回收？ | 不要讓「藏起來」等於「看不見」 |

各線的必答欄位、驗收條件與禁止事項，見 [`SKILL.md`](SKILL.md)。

---

## 安裝

### 作為 Agent Skill（支援 Agent Skills 的智能體）

把整個目錄放進目標智能體的 skills 目錄：

```text
<skills 目錄>/aigc-director-five-lines/
├── SKILL.md
└── references/
    └── seedance-prompt-director.md
```

- `SKILL.md` 開頭四行為 YAML frontmatter（`name`、`description`），**必須保留**，它決定技能何時被自動觸發。
- `references/` 的相對位置**必須保持**，否則階段 B 無法載入。

### 作為單檔提示詞（聊天型智能體）

直接把 [`docs/AIGC導演五線提示詞驗收-完整教學版.md`](docs/AIGC導演五線提示詞驗收-完整教學版.md) 整份貼入上下文——該檔已整合主技能、階段 B 模組與精簡系統提示。

---

## 使用

| 使用者這樣說 | 應有的反應 |
|---|---|
| 「做一個女孩在列車車廂裡害怕的影片鏡頭」 | **不要直接生成。** 先輸出驗收表，並優先追問最關鍵的 3–5 個缺口 |
| 「由你導演判斷，但開頭要藏住危險來源」 | 提出完整五條線＋資訊埋藏線的暫定方案，全數標為「由我暫定」，待確認後才輸出分鏡與提示詞 |
| 「畫面很漂亮但沒有情緒」 | 不擴寫風格詞；用反向診斷表定位斷點，修正上游分鏡後再重寫提示詞 |
| 「鏡頭接不上」 | 檢查動作線與剪輯線，補寫入點、出點、動作方向、焦點或聲音的承接條件 |

完整的階段 A 輸出範例見 [`examples/train-carriage-fear.md`](examples/train-carriage-fear.md)。

---

## 倉庫結構

```text
.
├── SKILL.md                                       # 主技能（階段 A）
├── references/
│   └── seedance-prompt-director.md                # 階段 B 模組
├── docs/
│   └── AIGC導演五線提示詞驗收-完整教學版.md        # 單檔完整版＋教學指南＋精簡系統提示
├── examples/
│   └── train-carriage-fear.md                     # 階段 A 實作示例
├── LICENSE
└── README.md
```

---

## 授權

[MIT](LICENSE)

---

## English

**aigc-director-five-lines** is a director-first validation workflow for AI image and video prompts.

Before any prompt is written, the workflow forces explicit answers for a **director's intent** (single dominant feeling, emotional arc, narrative position, exclusions) and **five lines**: action, emotion, camera, visual, and editing — plus a **buried-information line** for openings, foreshadowing, and delayed reveals.

Key rules:

- In strict mode, **any missing line blocks final prompt output**; the agent must ask the 3–5 most critical questions first, or record explicit assumptions as "director's provisional call" and wait for confirmation.
- Vagueness is banned: `cinematic`, `atmospheric`, `handheld`, `8K` may not replace narrative decisions. Every camera move needs a stated start position, facing, subject direction, path, trigger, and cut-out condition.
- Stage A ends at the storyboard (cut points, movement paths, reference choices, pacing). Stage B translates only — it may not add dialogue, characters, scenes, props, or alter approved decisions.
- Output is delivered as a fixed structure: `[Assets] → [Setting and style] → [Overview] → [Pacing] → [Shots] → [Close]`.

Install by dropping the folder into an agent's skills directory, or paste the single-file guide from `docs/` into any chat-based agent. Licensed under MIT.

---

## 適用範圍

影像提示詞、影片提示詞、分鏡、鏡頭序列、開場序列、成片診斷。
不適用於：純風格探索、單張插畫的構圖提案、與鏡頭無關的文案撰寫。