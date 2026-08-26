# Worked Example：Qwen3.8-27B think-off 迴圈病

> 原始診斷：@session:mentor/20260825_091335_6281d8（2026-08-25，desktop）。
> 本文件示範 lazy-teach 迴圈的每一拍長什麼樣——它是第一堂微課的原料，也是未來微課的格式標竿。

## 問題

本地 llama-server 跑 Qwen3.8-27B-OBLITERATED（Q8_0 GGUF），think 模式已關，但模型輸出仍無限循環——grilling session 第 3 輪起，同一塊問題原文逐字連貼三次。

## 迴圈實錄（每一拍對應 SKILL.md 五步）

| 拍 | 迴圈步 | 實際動作 | 觀察 |
|---|---|---|---|
| 0 | Orient | 調出故障 session 全文驗屍；確認症狀形狀（第 1–2 輪正常、第 3 輪起凍結、逐字重複） | 「典型文字退化」假設有了著力點 |
| 1 | Hypothesis | 「我猜是貪婪解碼的文字退化」——Holtzman et al. 2019〈The Curious Case of Neural Text Degeneration〉描述的現象：temperature=0 時模型一旦落入短句循環就理直氣壯地無限重複 | 可否證：查 server 實際取樣參數即可證偽 |
| 2 | Test | 找到 macOS LaunchAgent plist（`com.llama-server.qwen27b.plist`），讀取環境變數與啟動參數；對質四個疑點：模型資料夾實物、自訂 chat template、活著的 server 取樣參數、server log | Log 鐵證：事故 slot 的 KV cache 揹著 **172,573 tokens** 歷史 |
| 3 | Learn only what blocks | 只研究擋路的兩件事：① 貪婪解碼為何造成循環；② Hermes 重試時微調訊息（追加錯誤說明）為何讓新請求與舊快取的共同前綴對不齊 | 白話錨點：KV cache 像「一本書和書籤」——server 逐字比對新請求與存好的功課，一模一樣的部分直接沿用；前綴一變，書籤作廢，整段重算 |
| 4 | Adjust | 假設從「單純解碼參數」修正為「解碼參數 × 快取前綴錯位的疊加」；下一步實測方向隨之改變 | （session 於此轉入後續修復） |

## 教學拍點（若做成微課的三個洞）

1. **為何貪婪解碼會鬼打牆**：每步選最高分 → 一旦某短句被自選一次，機率結構鎖死成環。
2. **KV cache 共同前綴原理**：快取省的是「相同前綴的重算」，前綴錯位＝快取全滅＋更慢。
3. **為何重試機制反而火上加油**：自動重試追加內容 → 改變請求前綴 → 打碎快取對齊。

## 這個案例示範的事

- 症狀逐字抄錄（「同一塊 Q1–Q4 原文連續貼三次」）直接指向退化類型——Orient 的品質決定 Hypothesis 的命中率。
- 「一句話講清即走」（書籤比喻）就是 learn-only-what-blocks 的標準手感：不解釋 transformer 內積，只講擋路的那層。
- 每一拍的觀察都有落盤證據（plist 原文、log 行數、token 數）——Test 步的完成判準是「真實觀察」，不是推測。
