# 學習筆記：Zhu (2011) — VIX Futures 解析公式

論文：*An analytical formula for VIX futures and its applications*，Zhu (2011)

---

## 階段 1：VIX 是什麼？（直覺建立）

### VIX 的日常語言定義

VIX 是「市場對未來 30 天 S&P 500 波動幅度的預期」，又叫**恐慌指數（Fear Index）**。

數字怎麼讀：
- VIX = 20 → 市場預期 S&P 500 未來一年波動約 ±20%（年化）
- VIX = 80（2008 金融危機）→ 市場極度恐慌，預期劇烈震盪
- VIX 通常在 10–30 之間，平靜時期低，危機時飆高

**關鍵特性**：VIX 和股市通常**負相關**——股市跌，VIX 漲。

---

### 物理類比：VIX ≈「溫度」

把金融市場想成一鍋氣體：
- **股價** = 個別氣體分子的位置
- **波動率（volatility）** = 氣體的溫度（分子運動多劇烈）
- **VIX** = 對接下來 30 天平均溫度的預測

就像你看天氣預報預測「這週平均氣溫」，VIX 是市場用選擇權價格「預測」出來的未來波動溫度。

---

### 為什麼要交易 VIX Futures？

VIX Futures 是「約定在未來某天，以某個價格結算 VIX」的合約。

兩大用途：
1. **避險（Hedging）**：你持有大量股票部位，怕市場崩潰 → 買 VIX Futures，市場跌時 VIX 漲，幫你對沖損失
2. **投機（Speculation）**：你認為市場快大跌（或快回穩）→ 直接押注波動率方向

---

### VIX Futures 的有趣特性（論文 eq.12 的直覺）

**Spot VIX** vs **VIX Futures**：

- Spot VIX：「現在」市場的恐慌程度
- VIX Futures（到期 T）：「到 T 時刻」的 VIX 預期值

觀察：到期越遠的 VIX Futures，和現在 spot VIX 的相關性越低。

物理類比：你現在測到氣溫是 35°C（夏天），但你對「三個月後的平均溫度」的預測，不會只是 35°C，因為均值回歸（mean reversion）——溫度（波動率）長期會回到歷史平均。

---

## 接下來

→ **階段 2**：VIX 的數學定義（model-free formula，eq.1–2）
