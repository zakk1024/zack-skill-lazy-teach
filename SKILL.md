---
name: lazy-teach
description: 遇到真實問題時立刻開一堂分鐘級微課：迴圈陪修＋事後蒸餾成互動式 HTML 課件。
disable-model-invocation: true
argument-hint: "<問題描述（可省略）>"
---

# Lazy Teach

遇到真實問題，才學；理論只在擋路時出現；一堂課只服務一個問題。
方法源流見 [references/lazy-learning-method.md](references/lazy-learning-method.md)；worked example 見 [references/worked-example-qwen-loop.md](references/worked-example-qwen-loop.md)；術語以 [CONTEXT.md](CONTEXT.md) 為準。

## 入口判定（第一步，必做）

- **loop mode**——問題正在擋路（錯誤訊息、意外行為在眼前）：走「迴圈流程」。
- **distill mode**——「我剛修好了 X」或眼前沒有活著的問題：走「蒸餾流程」，把剛發生的除錯固化成微課。

判定不了就問一句。判定的同時做「暖身提議」（恰好一次，見下）。

## 工作區

中央工作區 `~/lazy-teach/`，lazily 建立：

```
~/lazy-teach/
├── INDEX.md            # 問題清單＋狀態＋主題計數（畢業追蹤靠它）
├── CONCLUSIONS.md      # 跨問題結論索引，一行一結論
└── NNNN-slug/          # 編號全域遞增
    ├── PROBLEM.md      # 症狀逐字原文＋環境＋blocking question（若有）
    ├── LOOP.md         # append-only 迴圈日誌
    └── lesson.html     # 互動微課（僅 distill 時產出）
```

首次啟動建立 `INDEX.md` 與 `CONCLUSIONS.md`；之後每個問題一個資料夾。

## 迴圈流程（loop mode）

### 1. Orient（定向）

把問題釘死再動手：症狀**逐字**抄下（錯誤原文，不轉述）、環境（版本／OS／相關設定）、已試過什麼與結果。寫入 `PROBLEM.md`。
✅ 完成判準：`PROBLEM.md` 存在，含逐字症狀。

### 2. Hypothesis（假設）

一句話：「我猜 X 因為 Y」。必須可否證——能設計出一個實測讓它死。寫入 `LOOP.md` 新的一節。
✅ 完成判準：有一條假設句，且說得出怎麼測它。

### 3. Test（小步實測）

最小動作驗證或否證假設。

- 使用者自己開 Terminal 打字；agent 導航、不代跑（除非明確說「執行」）。
- 每條 terminal command 附**白話拆解**：作用／成功樣貌／失敗意義。
- 觀察結果（無論成敗）append 進 `LOOP.md`。

✅ 完成判準：有真實觀察落盤，不是推測。

### 4. Learn only what blocks（只學擋路的）

只在「實測失敗且看不懂為什麼」時觸發：

- 研究範圍＝當前那一個症狀：精確搜尋錯誤原文、確切條件。
- 讀文件帶著目的；**能再測就停止研究**。
- 給使用者的解釋限一句話——卡住處講清即走，不展開。

✅ 完成判準：能填空「擋路的是 ___；所以下一個 hypothesis 是 ___」。

### 5. Adjust（調整假設）

修改的是假設，不是指令本身——指令只是假設的影子。回到第 2 步。

### 出口：deliberate close（hard gate）

任何終止必須二選一，寫進 `PROBLEM.md`：

- ✅ **one win**——具體、驗證過的成功（哪條命令通了、哪個輸出對了）。
- ⛔ **blocking question**——一句話寫死：「是什麼擋住了我」。

隨機中斷、挫折棄坑都不存在。使用者要中途停下：先把 blocking question 落盤再收——逃生艙永遠開著，但跳過必須留痕。
取勝 → 提議切 distill mode 固化戰果。受困 → blocking question 就是下一堂課的入口。

## 蒸餾流程（distill mode）

1. **收集原料**：剛修好什麼／當時卡在哪一句／哪個 hypothesis 轉了彎。session 紀錄裡有的自己讀，不叫使用者重打。
2. **產出微課**：用 [templates/lesson-template.html](templates/lesson-template.html) 生成 `lesson.html`——自包含單檔、繁中、零外部依賴、列印友好。三元件全上：回憶測驗（選擇題）、點擊揭示（先想再看）、症狀比對（貼真實錯誤訊息讓學習者對位）。只教一個洞。
3. **沉澱結論**：`CONCLUSIONS.md` 加一條——結論一句話＋為何。寫結論，不寫指令墳場。
4. **更新索引**：`INDEX.md` 記狀態與主題計數。
5. **畢業檢查**：同一主題第 3 次 → 必須提議畢業成正式 /teach 課程。提案不可省略；接不接受由使用者定。

✅ 完成判準：`lesson.html` 可開啟、兩份索引已更新、以一條 retrieval prompt 收尾。

## 暖身提議（每次啟動，恰好一次）

`CONCLUSIONS.md` 有存貨時，提議一次：「要不要花 60 秒，憑記憶講講最久沒碰的那條結論？」是邀請不是硬門；拒絕就直接進正題。這是本 skill 為長期保留付出的全部成本。

## 薄實證（承重規則，附出處）

- **即時回饋三件套**：回饋＝目標＋具體錯誤模式＋一個下一步；不做空泛讚美。[Hattie & Timperley 2007]
- **辨識 ≠ 召回**：重讀與聽講製造熟悉的錯覺；提問與測驗強迫召回——費力才是編碼。[Roediger & Karpicke 2006]
- **工作記憶有限**：一次一個概念、一堂一個洞；多洞並發就是超載。[Sweller et al. 2019]

> **誠實代價聲明**：以上規則服務當下的流暢度。長期保留（storage strength）靠間隔複習（約 +1／+3／+7／+14 天），那是 /teach 的管區。「今天會了」不等於「三天後還會」。

## 邊界

- 本 skill 無 MISSION、無 Roadmap、無 Curriculum。要系統性學一門東西 → /teach。
- 同主題第 3 次微課 → 提議畢業（見蒸餾流程第 5 步）。
