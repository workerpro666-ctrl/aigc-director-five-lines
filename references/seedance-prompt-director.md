# Seedance 提示詞總監（seedance-prompt-director）

本模組是 `aigc-director-five-lines` 的**階段 B**。階段 A 的導演提案（五線完整度驗收表＋標準分鏡）通過後，才進入本階段，把分鏡逐鏡轉譯為可直接送交 Seedance 的完整提示詞。

> **分工**：分鏡決定「切幾個鏡、每鏡交付什麼、誰從哪裡移動到哪裡、參考素材怎麼選、節奏多快」；提示詞總監負責「把這些翻譯成模型能理解的句子」。分鏡結束於剪點、動作路徑、參考選擇與節奏規劃；提示詞總監結束於可送出的完整提示詞。

## 定位與職權

- **只轉譯，不創作。** 不得新增角色、場景、情節、台詞、道具；不得改動已核准的動作、機位、鏡頭路徑、節奏、剪點、台詞與音效計畫。
- **缺漏退回。** 若分鏡缺起始機位、出鏡承接、音效來源或參考綁定，退回階段 A 補齊，不得自行填補。
- **輸入：** 通過驗收的分鏡表、Look／風格設定、實際參考素材檔案。
- **輸出：** 一個可直接送出的完整 Seedance 提示詞（全部鏡頭於同一次請求）＋送交參數與附件對應表＋不可妥協條件。

## 硬性規則

1. 送交模型的指令一律使用**英文**；使用者指定的台詞與畫面文字以原語言**逐字保留於引號內**。英文指令不改變影片語言。
2. 以 `Shot 1, Shot 2…` 標示多鏡，全部鏡頭放在**同一次請求**；總時長由工具參數控制，**不得寫每鏡起訖秒數**。
3. **只寫看得見、聽得見的事。** 不得寫心理狀態、意圖、主題、象徵（「她很孤獨」「內心的掙扎」「壓迫的氛圍」），必須轉譯為臉、手、身體、物件狀態與光線的變化。
4. **不得使用空泛風格詞**：cinematic、premium、atmospheric、史詩感、電影感。風格必須具體到「哪部片的哪一種夜景／哪位導演的取景與運鏡習慣／哪種攝影機與鏡頭／什麼色溫與飽和度」。
5. 每一鏡只能有**一個主要鏡頭運動**；主體動作與環境反應可同時發生。
6. **鏡頭術語不得取代運動描述。** `selfie view`、`close-up`、`handheld` 只說明視角、景別與拍攝方式；`camera follows` 必須補上跟誰、朝哪個方向、如何維持構圖。
7. **`Hard cut` 是剪輯指令，不得取代鏡頭運動**；剪點另以可見動作點描述。
8. **環境音與動作音效必須與畫面在同一次 Seedance 請求中生成**，不可改用獨立的音效生成步驟；非敘事 BGM 於後製加入，且不得蓋過台詞。
9. 全片重用同一組風格句子；每次改變的是「誰做了什麼」，不是每鏡換一次調色或換一位導演。

## 提示詞固定結構

`[Assets] → [Setting and style] → [Overview] → [Pacing] → [Shots] → [Close]`

- `[Assets]`：**只在有參考素材時出現**。依實際附件順序編號，逐一寫出該參考的角色，以及**不要沿用**的部分。
- `[Setting and style]`：空間結構、關鍵物件、景深、光線與調色、攝影機與鏡頭、色溫、飽和度、主光方向、陰影深淺、可見材質。
- `[Overview]`：誰在何處、整段可見事件順序，並聲明所有鏡頭一次生成。
- `[Pacing]`：整體節奏，以及哪些節拍較快或較長。
- `[Shots]`：**逐鏡重複完整結構，不得以省略號帶過**。
- `[Close]`：一致性、參考還原、保留同步台詞與音效、禁止浮水印／字幕條／片尾卡。

```text
[Assets]
@Image1 is for {character} appearance, hair, and costume. Do not take the background or pose.
@Image2 is for {location} spatial structure, materials, and light. Do not take the people in the frame.
@Image3 is for {object} shape and material. Do not take the scene around it.
@Audio1 is for {character} timbre and speaking manner. Do not take the words in the sample.

[Setting and style]
{location layout, key objects, spatial depth, and the areas used by the action and camera movement}.
Light and grade like {a type of scene from a film}, with a little of {a director}'s usual framing / camera-move habits. Shot on {camera + lens}: {film grain / clean digital}, {focal length or depth of field}. {color temperature}, saturation {high/low}. Key light from {direction}, shadows {deep/shallow}. Visible materials: {wet glass, old wood, metal reflections}.

[Overview]
{who} is in {where}; {the visible sequence of events across the whole video}. Generate all shots below together in one request.

[Pacing]
{overall rhythm and which beats are quicker or longer, matched to the action and dialogue}.

[Shots]
Shot {n}: Start at {shot size, camera height, and angle}. The camera {one main movement, direction, path, and pace}, ending with {final framing or maintained position relative to the subject}. {Who acts from where to where, with what speed and range; the visible physical result}. {Exact dialogue and speaker, when present; synchronized action sound and ambience}. Cut on {a visible action point that joins the next shot, or completes the video}.

[Close]
Keep character, wardrobe, setting, and visual style consistent with the written design and any supplied references. Accurately reproduce specific products, logos, webpages, phones, and UI from their supplied references when required. Keep the planned synced dialogue, ambient sound, and action SFX; no watermark, caption bar, or end card.
```

**每鏡必填七項**（缺一即視為分鏡未通過，退回階段 A）：

起始景別／機位高度／角度 → 主要鏡頭運動（方向、路徑、速度）→ 結束構圖或相對主體位置 → 主體動作（由哪到哪、速度、幅度、可見結果）→ 同步聲音（環境音、動作音效、台詞與口型）→ 剪點。

## 五線 → Seedance 欄位對照

| 五線欄位 | 轉譯到 |
|---|---|
| 動作線：前態／起因／動作／物理結果／下一條件 | `[Overview]` 事件順序 ＋ Shot 的主體動作（由哪到哪、速度、幅度、可見結果）＋ `Cut on …` |
| 情緒線：微表情／身體變化 | Shot 內可見的臉部、手部、呼吸、姿態、重心變化；情緒名詞一律刪除 |
| 攝影線：起始機位／拍攝朝向／主體方向／鏡頭路徑／運動觸發／視覺結果／出鏡承接 | Shot 的 `Start at …`、`The camera …`、`ending with …`、`Cut on …` |
| 視覺線：第一秒主訊息／焦點景深／遮擋／構圖／光色 | `[Setting and style]` 的光線、鏡頭、景深、材質；Shot 的起始景別與焦點落點 |
| 剪輯線：入點／新資訊／出點／接法／節奏時長 | `[Pacing]` ＋ 每鏡 `Cut on …` ＋ 下一鏡 `Start at …` 的承接 |
| 資訊埋藏線：線索本體／觀看程度／藏置位置／隱藏理由／揭露機制 | 以遮擋、失焦、暗部、畫面邊緣、畫外聲或前景物具體寫入；不得只寫「藏起來」 |

## 參考素材綁定

角色與產品必須使用已核准的參考圖；場景、光線、風格可用一致的文字描述。每一條參考都要寫出**它的角色**與**不要沿用的部分**：

- 角色參考 → 身分、髮型、服裝；不要背景與原姿勢。
- 場景參考 → 空間結構、材質、光線；不要畫面中的人物。
- 物件參考 → 形狀與材質；不要周遭場景。
- 音色參考 → 音色與說話方式；不要樣本裡的台詞。
- 全單元光線與調色參考 → 光、調色與材質；不要人物的動作。

只綁定**實際附上的**檔案；未附上的參考行必須刪除。

## 音訊設計

1. **台詞與口型**：寫出說話者與逐字台詞，要求口型同步；工具支援音色參考時綁定 `@AudioN`。
2. **環境音**：雨、街道、機房低頻、房間殘響等空間聲。
3. **動作音效**：與可見動作同步的鍵盤、門、腳步、衣物、按鈕、杯子、呼吸、警報。
4. **畫內音源音樂**：僅當畫面中存在真實音源（收音機、手機、賣場廣播）時才寫入生成提示詞。
5. **非敘事 BGM** 於後製加入；不得因此移除同步台詞、環境音或動作音效。「不要旁白」不等於完全靜音。
6. 使用者要求完全靜音時，不生成也不加入任何聲音，讓畫面獨自承擔鉤子。

## 首兩秒（社群影片）

資訊、運動、聲音三者同時成立並指向同一個重點：一句可讀懂的重點、立刻可見的動作或鏡頭運動、與事件同步的聲響。避免以空景、慢速建立鏡頭或淡入開場。

## 鏡頭密度

- 一般**每 2–3 秒一切**，每次切換帶來新的動作、視角或資訊。
- 快節奏社群素材約 **1–2 秒**；情緒鋪陳、完整表演、需閱讀資訊的鏡頭**給足時間**。
- 切得快不會讓動作更有力；每鏡的主體動作與鏡頭運動必須各自設計，不得以密集剪接掩蓋動作不足。

## 送出前檢查

1. 全部指令為英文、無未填佔位符、無多餘參考行。
2. 模型、總時長、比例、解析度、附件、音訊符合**當前工具 schema**（不得依模型名稱推測限制）。
3. 可容於一次請求的鏡頭全部放在同一請求；拆分須寫出具體理由。
4. 只讀提示詞即可還原整段意圖，無須參照分鏡或其他文件。
5. 每個參考都對應真實附件並寫明角色。
6. 角色、服裝、場景、風格一致；品牌、產品、網頁、UI 依參考準確還原。
7. 同步台詞、環境音、動作音效保留；無浮水印、字幕條、片尾卡。

## 輸出格式

```markdown
### Seedance 提示詞｜[單元／鏡號範圍]

**送交參數：** 模型〔取自當前工具 schema〕／總時長 X 秒／比例 X／解析度 X／音訊 開或關／附件對應 @Image1…、@Audio1…

**完整提示詞：**
[Assets] … [Close]（依固定結構逐鏡完整，不省略任何一鏡）

**不可妥協條件：**
- [最多 3–5 條，優先保留會令五線失效的約束]
```

## 階段 B 禁止事項

- 不得新增台詞、角色、場景或道具，或改動已核准的動作、機位、鏡頭路徑、節奏與剪點。
- 不得以風格詞、情緒名詞或鏡頭術語取代可見動作與可執行的鏡頭運動描述。
- 不得為每鏡重寫一次風格；不得讓多個同等強度的主體爭搶注意力。
- 不得只交付 BGM 而缺少已規劃的同步音效。
- 不得將「資訊埋藏」處理成後續無法回收、也無法被觀眾潛意識感知的隨機背景物。