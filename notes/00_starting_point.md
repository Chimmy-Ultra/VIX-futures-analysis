# Stage 0 — 起點對齊

目標:把你「交易過 VIX」的實務直覺,對應到這篇 paper 會用到的學術詞彙。
不教新東西,只是把你已有的知識**重新貼標籤**。

預估時間:30 分鐘。

---

## 1. 你已經會的(從交易經驗來的)

下面這些只要你會打勾就算 Stage 0 完成。

- [ ] VIX 是「S&P 500 預期波動率」,俗稱恐慌指數
- [ ] VIX 跟 S&P 500 通常**反向**(股跌 VIX 漲)
- [ ] VIX 本身**不能直接交易**(它只是個指數),要交易得透過 VIX **期貨**、選擇權、或 ETF(VXX、UVXY、SVXY)
- [ ] 長期持有 VXX 會「越來越值錢嗎?」→ 不會,通常會虧,因為 **contango 損耗**

如果上面有不確定的,跟我說,我補。

---

## 2. 把你的詞彙翻成 paper 的詞彙

| 你交易時用的詞 | 學術 / paper 用詞 | 含義 |
|--------------|-----------------|------|
| VIX 指數 | **spot VIX** 或 `VIX_t` | 當下的 VIX 數值 |
| VIX 期貨 | **VIX futures** 或 `F_t(T)` | 在時間 t 簽下、約定 T 時刻交割的合約 |
| 到期日 | **maturity** 或 `T` | 期貨到期那天 |
| 距離到期還有多久 | **time to maturity** 或 `τ = T - t` | 多半用 τ 表示 |
| VXX 一直跌 | **roll yield / negative carry from contango** | 期貨曲線往上斜時,持有近月會持續吃虧 |
| 期貨曲線 | **term structure of VIX futures** | 不同到期日對應的期貨價格 |
| 「平靜時 contango,恐慌時 backwardation」 | **term structure regime** | paper 會直接用這詞 |
| 「真實機率」(這檔 1 個月後漲多少) | **physical measure** `P` | 真實世界的機率分布 |
| 「定價用的機率」(算理論價時用) | **risk-neutral measure** `Q` | 數學上方便、不是真實機率 |

> 注意:**所有期貨定價公式裡的「期望值」`E[...]` 都是在 Q(risk-neutral)下,不是 P**。
> 這在 Stage 2 會再講為什麼。現在只要知道:看到 `E^Q[...]` 不是「我預期會怎樣」,是「為了無套利,理論價必須這樣算」。

---

## 3. 為什麼這篇 paper 存在?(一句話)

你交易 VIX 期貨時,**它的「公允價格」是怎麼決定的?**

- 一般股票期貨好算:`F = S × e^(rT)`(無套利)
- VIX 不能直接買賣 → **沒有 spot-futures 套利關係**
- 結果就是:VIX 期貨價格沒有簡單公式,以前只能用 Monte Carlo 模擬

**Zhu & Lian (2012) 的貢獻 = 在 Heston 模型假設下,給出 VIX 期貨的封閉解析公式(closed-form formula)。**

封閉解 = 不用模擬,直接帶數字進去算。

---

## 4. Stage 0 結束時你應該能回答

1. 為什麼 VIX 不能用 `F = S × e^(rT)` 算期貨?
2. 「risk-neutral measure」跟「我預期 VIX 會漲」是同一件事嗎?
3. 這篇 paper 想解決什麼**具體問題**?

如果都答得出來 → 進 Stage 1。
答不出來 → 跟我說哪題卡,我再補。
