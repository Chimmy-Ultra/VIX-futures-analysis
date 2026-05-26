# 帶讀講義 架構藍圖 — Zhu & Lian (2011), "An Analytical Formula for VIX Futures"

> 這是**架構文件**，不是講義本身。目的是讓後續寫作者能照著產出完整的 Traditional-Chinese 帶讀講義，而不需要重新設計結構或重新推導。
> 所有方程式編號沿用論文原始編號 (1)–(14)、(A1)–(A10)。每個式子都附上「已對照原文驗證」的 LaTeX-able 形式。
>
> **書目核實**：封面寫 *Journal of Futures Markets* 31(6), 547–571，但 PDF 內頁 running head 與版權頁實際為 **Vol. 32, No. 2, 166–190 (2012)**，DOI 10.1002/fut.20512，作者為 **Song-Ping Zhu 與 Guang-Hua Lian**（不只 Zhu 一人，亦非單一通訊作者署名）。Received Aug 2009 / Accepted Dec 2010。講義引用時建議以內頁為準，或同時標注兩組卷期。**這是給讀者的第一個 flag。**

---

## 0. 讀者畫像與校準（寫作者必讀，先內化再動筆）

- **金融**：2 年衍生品交易/結構經驗。VIX、futures、option、hedging、no-arbitrage、risk-neutral pricing、term structure、Jensen 不等式（入門層次）——**全部視為母語，一律不解釋、不編列篇幅**。把這些當新知是侮辱。
- **數學**：微積分、機率扎實；**但不常用隨機微積分**。Itô lemma、SDE、quadratic variation、Feynman–Kac 是「真正的知識缺口」，要從他的微積分/機率底子**慢慢搭起來**，不可跳步。
- **物理**：用物理直覺思考，要的是**物理學家真的會認的對應**（BS≅熱方程、Feynman–Kac≅虛時間路徑積分/propagator、CIR≅OU/Langevin、affine MGF≅二次作用量的 Gaussian 路徑積分、Riccati≅Hamilton–Jacobi）。明令「不要為了物理而物理」。**沒有真實對應的章節就不要放物理 box。**
- **內容重心（讀者兩個都要）**：(1) 完整推導鏈 eq1→eq5→積分公式→MGF ODE，隨機微積分步驟不准跳；(3) 架構與直覺——為什麼這樣建模、每個建模選擇買到什麼、每個結果的 "so what"。
- **被退稿的前一版踩的雷**（務必避開）：把市場比作「一鍋氣體、股價=分子位置、VIX=溫度」（讀者點名動怒）；把 VIX 解釋成「市場很亂」、futures 解釋成「對未來的猜測」；整體 pitch 太低。**現存 `learning-notes.md` 第 22–29、52 行正是這個壞類比，寫作時不要沿用，可作為反面教材。**

---

## 1. Narrative spine（敘事主軸）

**一段話主軸**：
整篇論文是一條「把一個看似需要完整機率分布的期望值，壓縮成一維實積分」的路。核心觀察是 model-free 的 VIX 平方在 SVJJ 模型下**塌縮成瞬時變異數 V_T 的仿射（affine）線性函數** `VIX_T² = a·V_T + b`（eq 5）。於是 VIX futures 價格 `F = E^Q[√(aV_T+b)]`（eq 7）這個「平方根的期望」原本卡在兩個難點上——(i) 平方根不是多項式、沒有現成的 transform；(ii) V_T 的密度沒有封閉式——Zhu 用兩把鑰匙各破一關：用 **Schürger (2002) 的 Laplace/Gamma 型恆等式**把 √x 寫成對 `1−E[e^{−sx}]` 的積分（把「平方根」轉成「MGF 的線性疊加」），再用 **V_T 的 affine MGF**（其 C、D、A 三條 ODE，Riccati 結構）給出 `E[e^{−sx}]` 的封閉式。兩者一拼，得到只含**一維實積分、實值且光滑被積函數**的封閉定價公式（eq 9 / A10），完全繞開複數 Fourier 反演。剩下半篇是這把「精確尺」的兩個用途：(2.3) 量出 Lin (2007) 與 Zhang–Shu–Brenner (2010) 的 convexity 近似誤差有多大；(3) 用 MCMC 在 SV/SVJ/SVVJ/SVJJ 四個嵌套模型上做實證，回答「加 jump 到底值不值」。

**3 個 load-bearing 結果（全篇掛在這三根樑上）**：
1. **eq (5)–(6)**：`VIX_T² = (a V_T + b)×100²`，a、b 為模型參數的封閉表達式。這把「model-free 的 VIX」鎖進「SVJJ 的單一狀態變數 V_T」。**沒有這步，後面什麼都做不了。**
2. **eq (9) / (A10)**：一維實積分定價公式 = 「Schürger √x 恆等式」×「affine MGF」。這是論文的標題成果。
3. **eq (10)–(11) / (A3)–(A6)**：MGF 的 affine 形式 `f = exp(C + D·V + A)` 與 C、D、A 三條 ODE（D 是 Riccati）。這是讓第 2 點可計算的引擎。

> 收斂主題（可在前言與結論各點一次）：**affine/可解性**是貫穿全篇的物理-數學主題——線性 generator → 指數型 MGF → Riccati ODE，正是「二次作用量可積分」在機率語言下的版本。

---

## 2. Section-by-section outline（逐節大綱）

> 標記說明：每節給 (a) 標題 (b) 涵蓋 (c) 出現的精確方程（原編號＋已驗證 LaTeX 形式）(d) 要展示的推導步驟（尤其隨機微積分）(e) 是否放物理對應＋哪一個＋為何 load-bearing (f) 深度註記（assume vs derive）。

### 前言：三個等號走完全篇
- **(a)** 〈前言：一條從 model-free VIX 到一維積分的路〉
- **(b)** 給出全篇 roadmap，把三根樑（eq5 / eq9 / MGF-ODE）先擺上桌；用一行「等號鏈」讓讀者看到終點。明確列出對讀者的假設（金融不講、隨機微積分要補、物理只在真對應處出現）。**現存 `.tex` 已有此頁雛形且方向正確（line 84–95 的假設框可沿用）。**
- **(c)** 預告式列出 eq (5)、(7)、(9)；不展開。
- **(d)** 無推導。
- **(e)** 無物理 box（這裡放會變裝飾）。
- **(f)** 純導覽。**修正現存 `.tex` line 117–120 的等號鏈**：該處把中間步驟寫成 `φ(-au) du`、積分變數 `u`，與論文 eq (9)/(A10) 不符。正確中間形態見 §3 第 4 步——積分變數是 **s**，被積函數是 `[1 − e^{−sb} f(−sa; …)]/s^{3/2}`，MGF 餵入的引數是 **−sa**（不是 −au）。寫作者務必用 §3 的版本覆蓋之。

### 第 1 節：定價的舞台 — model-free VIX 與它的兩種寫法
- **(a)** 〈VIX 的 model-free 定義與 log-contract 改寫〉
- **(b)** 從 CBOE 的 model-free 定義（eq 1）出發，說明它「為何」能寫成 risk-neutral 對 log-contract 的期望（eq 2）。重點不在「VIX 是什麼」（讀者已知），而在**「為什麼這個離散加總 = −(2/τ̄)E^Q[ln(S_{t+τ̄}/F)]」這個 model-free 結果的數學內容**——這正是後面把 VIX 接到模型上的接口。
- **(c)**
  - eq (1)：
    `VIX_t^2 = ( (2/\bar\tau) \sum_i (\Delta K_i / K_i^2) e^{r\bar\tau} Q(K_i) - (1/\bar\tau)[F/K_0 - 1]^2 ) \times 100^2`，其中 `\bar\tau = 30/365`。
  - eq (2)：
    `VIX_t^2 = -\frac{2}{\bar\tau} E^{\mathbb Q}\!\left[\ln\frac{S_{t+\bar\tau}}{F}\,\middle|\,\mathcal F_t\right]\times 100^2`，`F = S_t e^{r\bar\tau}`。
- **(d)** **要展示**（這是讀者「想看的隨機微積分起點」之一）：log-contract 與「連續加權 OTM option」之間的 Carr–Madan 型 static replication 骨架，說明 eq(1)→eq(2) 是「對 payoff 做二階泰勒/Breeden–Litzenberger」的結果。**深度判斷見 §6 開放問題 Q1**——可只給骨架不給全證。**注意**：論文 eq(2) 本身就是 model-free 的，**尚未**用到 SVJJ；真正的隨機微積分在第 2 節 V_t 動態才正式登場。
- **(e)** **不放**獨立物理 box。（log-contract 沒有乾淨的標準物理對應；硬套會違反「不要為物理而物理」。）
- **(f)** eq(1) 視為已知背景（讀者天天看 CBOE 方法論），**只**花力氣在 eq(1)→eq(2) 的數學橋。不解釋 VIX 的金融意義。

### 第 2 節：SVJJ 模型 — 把 V_t 寫成可解的隨機過程
- **(a)** 〈SVJJ 動態：從 Heston 到雙重跳躍，以及隨機微積分工具箱〉
- **(b)** 給出 P-測度（eq 3）與 Q-測度（eq 4）下 (S_t, V_t) 的 SDE，逐項拆解：drift、CIR-型擴散 `σ_V√V dW^V`、相關係數 ρ、Poisson 跳躍 N_t（強度 λ）、價格跳 `Z^S`、變異數跳 `Z^V`、jump 補償項 `−S_t μ̄ λ dt`。解釋 P→Q 的測度變換對哪些參數動手（diffusive variance risk premium `η_V = κ^Q − κ`、jump 風險 `η_J = μ_S^Q − μ_S`；σ_V、ρ、κθ、λ 與其餘 jump 參數在兩測度下不變）。**這節是隨機微積分的主補課現場。**
- **(c)**
  - eq (3)（P 測度）：
    ```
    dS_t = S_t(r_t+γ_t)dt + S_t√V_t dW_t^S + d(Σ_{n=1}^{N_t} S_{τ_n^-}[e^{Z_n^S}-1]) - S_t \bar\mu λ dt
    dV_t = κ(θ-V_t)dt + σ_V√V_t dW_t^V + d(Σ_{n=1}^{N_t} Z_n^V)
    ```
    跳躍規格：`Z_n^V ~ exp(μ_V)`（指數分布），`Z_n^S | Z_n^V ~ N(μ_S + ρ_J Z_n^V, σ_S^2)`，`\bar\mu = e^{μ_S+½σ_S^2}/(1-ρ_Jμ_V) - 1`，`E[dW^S dW^V] = ρ\,dt`，`γ_t` 為 total equity premium。
  - eq (4)（Q 測度）：同結構，drift 改為 `S_t r_t dt`、`κ^Q(θ^Q - V_t)dt`，跳躍與 BM 改測度版，`\bar\mu^{\mathbb Q} = e^{μ_S^{\mathbb Q}+½σ_S^2}/(1-ρ_Jμ_V) - 1`。
- **(d)** **必展示、不可跳**：
  1. **Itô lemma 從一階泰勒＋(dW)²=dt 建起**：先用讀者的普通微積分泰勒展開，補上 `(dW_t)^2 = dt`、`dt·dW=0`、`(dt)^2=0` 的乘法表（quadratic variation 的操作型定義），導出 `df = (f_t + μ f_x + ½σ² f_{xx})dt + σ f_x dW`。
  2. **跳躍-擴散版 Itô**：補上跳躍項 `f(x+Z)-f(x)` 的補償與 compensator `λE[·]dt`，說明 `−S_t\bar\mu λ dt` 為何讓 `e^{-rt}S_t` 成 Q-鞅。
  3. **CIR 過程的定性**：mean reversion κ(θ−V)、`√V` 擴散保證 V≥0、Feller 條件一句帶過。
- **(e)** **放物理 box（真對應，load-bearing）**：
  - **CIR ≅ Ornstein–Uhlenbeck / Langevin（含態依賴噪聲）**：`dV = κ(θ−V)dt + σ_V√V dW` 的漂移就是線性回復力（OU/Langevin 的 −γ(x−x₀)），差別只在乘性噪聲 `√V`。這讓讀者用 Langevin 直覺理解 mean reversion 與穩態分布（CIR 穩態為 Gamma，OU 為 Gaussian）。**load-bearing**：第 4 節 MGF 的指數-仿射可解性，根源就是這個線性漂移 + 二次擴散結構。
  - **（可選）Itô 的 (dW)²=dt ≅ Wiener 路徑的二次變差**：點到為止，連到物理人熟的「布朗運動均方位移 ∝ t」。
- **(f)** eq(3)/(4) 的金融動機（為何要 stochastic vol、為何要 jump）只用**一兩句**點出「買到什麼」（fat tail、leverage、vol spike），主力放在**隨機微積分機制**。不講 Brownian motion 是什麼（假設已知），但 Itô lemma 要從頭搭。

### 第 3 節：第一根樑 — VIX² 塌縮成 V_T 的仿射函數（eq 5–6）
- **(a)** 〈為什麼 VIX² 是 V_T 的線性函數：affine 結構的第一次現身〉
- **(b)** 把 eq(2) 的 `−(2/τ̄)E^Q[ln(S_{t+τ̄}/F)]` 在 eq(4) 動態下**顯式算出**，得到 eq(5) 的線性關係。核心訊息：**model-free 的 VIX² 在這個模型下只剩一個隨機自由度 V_t**，a、b 全是常數參數。這是把「無窮維的密度問題」降到「一維狀態變數」的關鍵塌縮。
- **(c)**
  - eq (5)：`VIX_t^2 = (a V_t + b)\times 100^2`。
  - eq (6)：
    ```
    a = (1 - e^{-κ^Q \bar\tau})/(κ^Q \bar\tau),   \bar\tau = 30/365
    b = (θ^Q + λμ_V/κ^Q)(1-a) + λc
    c = 2[\bar\mu^{\mathbb Q} - (μ_S^Q + ρ_J μ_V)]
    ```
- **(d)** **必展示**，這是讀者點名要的「eq1→eq5 推導」：
  1. 由 Itô 對 `ln S_t` 取（含跳躍）：`d ln S_t = (r - ½V_t)dt + √V_t dW^S + [跳躍貢獻] − \bar\mu λ dt`。
  2. 取 `E^Q[ ∫_t^{t+τ̄} · ]`，用 **CIR 的條件期望** `E^Q[V_u|\mathcal F_t] = θ^Q + (V_t-θ^Q)e^{-κ^Q(u-t)}`（這步要單獨導：對 `dV` 取期望得到 `dm/du = κ^Q(θ^Q − m)` 的一階線性 ODE，解之）。
  3. 對 u 積分 `∫_t^{t+τ̄}E^Q[V_u]du` → 產生 `(1-e^{-κ^Q τ̄})/(κ^Q τ̄)` 即係數 a。
  4. 跳躍項貢獻 `E[Z^S]`、`E[Z^V]` 與補償項組合成 b 與 c 中的 `λμ_V/κ^Q`、`\bar\mu^Q`、`μ_S^Q+ρ_J μ_V`。
  - **subtle 點要 flag**：b 中 `λμ_V/κ^Q` 來自「變異數跳躍對 V_u 期望的長期貢獻」；c 中 `μ_S^Q + ρ_J μ_V` 來自價格跳躍對 `E[ln S]` 的貢獻（含跳躍 size 對 V 跳的迴歸 ρ_J）。論文把這些直接報結果（"as shown in Lin 2007 and Duan and Yeh 2007"），**講義應補上中間的期望計算**，否則讀者看不到 a、b 怎麼長出來。
- **(e)** **放物理 box（真對應，load-bearing）**：
  - **「VIX² 是 V 的線性觀測量」≅ 仿射可觀測量 / 線性響應**：在 affine 框架下，一個對未來路徑的線性泛函的期望，仍是當前狀態的線性函數——這正是後面整個 transform 方法能 work 的原因。可與「線性系統的期望響應仍線性」對照。**load-bearing**：直接決定了 eq(7) 的 √(aV_T+b) 形態，是 Schürger 技巧的施力點。
  - （**不要**在這裡放任何「溫度/氣體」類比。）
- **(f)** a、b 的**最終表達式**照抄論文（已驗證），但**中間期望**要 derive。強調「given VIX 可反解 V_t = (VIX²/100² − b)/a」這個實務 hook（footnote 2 的內容），因為它讓 V 成為可由市場直接讀出的「independent variable」。

### 第 4 節：第二、三根樑 — 平方根的期望 → 一維實積分（eq 7–11 + Appendix A）
> **全篇技術核心。建議拆三個 subsection。**

#### 4.1 問題設定：F = E^Q[√(aV_T+b)] 為何難
- **(c)** eq (7)：`F(t,T) = E^{\mathbb Q}[VIX_T|\mathcal F_t] = E^{\mathbb Q}[\sqrt{aV_T+b}\,|\,\mathcal F_t]\times 100`。
- **(b/d)** 點出兩個障礙：(i) √ 非多項式、不能靠對 MGF 取導數得到；(ii) V_T 在 SVJJ 下密度無封閉式。說明**為何 F 是 Q-鞅**（Carr–Wu 2006；no-arb + 連續 marking to market）——讀者已懂鞅，故一句帶過，重點是「所以 futures price = 風險中性期望」這個接口。
- **(e)** 無新物理 box。

#### 4.2 鑰匙一：Schürger 的 √x Laplace 恆等式（**讀者特別要核實的點**）
- **(c)** eq (A9)（Schürger 2002）:
  `\sqrt{x} = \frac{1}{2\sqrt\pi}\int_0^\infty \frac{1 - E[e^{-sx}]}{s^{3/2}}\,ds`
  （對隨機變數 x 取期望的版本；其純量根 kernel 為 `\sqrt{x}=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-e^{-sx}}{s^{3/2}}ds`。）
- **⚠️ 必須向讀者 flag 的 discrepancy（讀者第二手記憶有誤）**：讀者預期可能是 **Gaussian-integral** 形式 `√x = (2/√π)∫_0^∞ e^{-xu²}du`。**這是錯的**——該 Gaussian 式其實對應 `1/√x`（即 `\int_0^\infty e^{-xu^2}du = \frac{1}{2}\sqrt{\pi/x}`，給的是 x^{-1/2}）。Zhu 實際用的是 **Schürger (2002) 的 Laplace/Gamma 型恆等式**（eq A9），透過 `s^{-3/2}` 權重與 `1−e^{-sx}` 把**正根** √x 表示出來。其數學根源是 Gamma-function/Frullani 型積分：`\int_0^\infty \frac{1-e^{-sx}}{s^{3/2}}ds = 2\sqrt{\pi}\,\sqrt{x}`（可由 `Γ(-1/2)=-2\sqrt\pi` 的解析延拓或分部積分驗證）。**講義應給出這個恆等式的簡短自證**（一行 Gamma 或分部積分），並明說「這不是 Gaussian √x 公式，請勿混用」。
- **(d)** 推導路徑：把 eq(7) 的 √ 用 A9 換掉，**Fubini 交換期望與 s-積分**（論文明寫 "after interchanging the expectation and integral using Fubini's theorem"），於是 `E[√x] = \frac{1}{2\sqrt\pi}\int_0^\infty\frac{1 - E[e^{-sx}]}{s^{3/2}}ds`。關鍵轉化：**`E[e^{-sx}]` 就是 x 的 MGF 在 −s 處的值**——平方根被「線性疊加無窮多個 MGF」取代。代入 `x = aV_T+b`：`E[e^{-s(aV_T+b)}] = e^{-sb}E[e^{-saV_T}] = e^{-sb}f(-sa;t,τ,V_t)`。
- **(e)** **放物理 box（真對應，load-bearing）**：
  - **Laplace/Frullani 表示 ≅ 用「指數模態」展開非多項式可觀測量**：把 √ 這種 hard nonlinearity 拆成對 Laplace kernel `e^{-sx}` 的加權積分，與物理上「用本徵模/heat-kernel 疊加表示一般函數」同構。**load-bearing**：正是這一步把問題轉成「只需要 MGF」，沒有它就回到複數 Fourier 反演。
- **(f)** A9 給**完整自證**（讀者明確要求核實），並把 discrepancy 寫進正文或顯眼 remark box。

#### 4.3 鑰匙二：V_T 的 affine MGF 與 C、D、A 的 ODE（Riccati）
- **(c)**
  - eq (A1)：`f(\phi;t,\tau,V_t)=E^{\mathbb Q}[e^{\phi V_T}|\mathcal F_t]`，`τ=T-t`；characteristic function = `f(i\phi;·)`。
  - eq (10)：`f(\phi;t,\tau,V_t)=e^{C(\phi,\tau)+D(\phi,\tau)V_t+A(\phi,\tau)}`。
  - eq (A2)（Feynman–Kac backward PIDE）:
    `-f_\tau + κ^{\mathbb Q}(θ^{\mathbb Q}-V)f_V + \tfrac12 σ_V^2 V f_{VV} + λ E^{\mathbb Q}[f(V+Z^V)-f(V)\mid\mathcal F_t]=0`，初值 `f(\phi;t,0,V)=e^{\phi V}`。
  - eq (A4) 三條 ODE（代入 A3 後分離 V 的冪次得到）:
    ```
    D_τ = -κ^{\mathbb Q} D + ½ σ_V^2 D^2          (Riccati)
    C_τ = κ^{\mathbb Q} θ D
    A_τ = λ E^{\mathbb Q}[e^{D Z^V} - 1 | \mathcal F_t]
    ```
    初值 (A5): `C(\phi,0)=0, D(\phi,0)=\phi, A(\phi,0)=0`。
  - eq (A6)=eq (11) 解:
    ```
    A(\phi,\tau)=\frac{2μ_Vλ}{2μ_Vκ^{\mathbb Q}-σ_V^2}\ln\!\Big(1+\frac{\phi(σ_V^2-2μ_Vκ^{\mathbb Q})}{2κ^{\mathbb Q}(1-μ_V\phi)}(e^{-κ^{\mathbb Q}τ}-1)\Big)
    C(\phi,\tau)=\frac{-2κθ}{σ_V^2}\ln\!\Big(1+\frac{σ_V^2\phi}{2κ^{\mathbb Q}}(e^{-κ^{\mathbb Q}τ}-1)\Big)
    D(\phi,\tau)=\frac{2κ^{\mathbb Q}\phi}{σ_V^2\phi+(2κ^{\mathbb Q}-σ_V^2\phi)e^{κ^{\mathbb Q}τ}}
    ```
- **(d)** **必展示、不可跳（讀者核心訴求）**：
  1. **Feynman–Kac 把期望 (A1) 變成 PIDE (A2)**：對 `f(τ,V)=E[e^{\phi V_T}|V_t=V]` 用（跳躍-擴散）Itô + 鞅性（drift 必須為零）導出 backward equation。要解釋 `-f_τ`（τ=T−t 的時間反向）、生成元 `κ^Q(θ^Q−V)∂_V + ½σ_V²V∂_{VV}` 的擴散部分、`λE[f(V+Z^V)−f(V)]` 的跳躍積分項。
  2. **affine ansatz (A3) 代入分離變數**：代 `f=e^{C+DV+A}`，算 `f_τ=(C_τ+D_τV+A_τ)f`、`f_V=Df`、`f_{VV}=D²f`，跳躍項 `E[e^{DZ^V}-1]`。**按 V 的冪次比對係數**：V¹ 係數給 D 的 Riccati、V⁰ 係數分給 C（drift κθ 部分）與 A（jump 部分），得 (A4)。
  3. **解 Riccati D**：說明這是常係數 Riccati，可用「指數代換 / 化成線性二階」標準法解出 (A6) 的 D；再積分得 C、A（C 是對 D 積分，A 用 `Z^V~exp(μ_V)` 算 `E[e^{DZ^V}]=1/(1-μ_V D)` 後積分）。
  - **subtle 點要 flag**：(i) A 的解只在 SVJJ（V 有跳）時非零；SV 模型 λ=0 ⇒ A≡0、C/D 退回 Heston。(ii) `E[e^{DZ^V}]=(1-μ_V D)^{-1}` 用到指數分布 MGF，需 `μ_V D<1` 的收斂條件，講義應一句點出。(iii) 論文把 A4→A6 的積分細節留給 Appendix「the solutions to these ODEs are」，講義對 D 的 Riccati 解**至少示範一次**完整解法，C、A 可給「代入驗證」級別。
- **(e)** **放物理 box（兩個都真、都 load-bearing）**：
  - **Feynman–Kac ≅ 虛時間 Schrödinger / heat propagator（路徑積分）**：期望 `E[e^{\phi V_T}]` 滿足的 backward PIDE 就是帶勢/帶 source 的擴散方程；`f` 是 propagator（轉移核的 transform）。這是物理人最認得的對應，且**直接解釋**為何「指數型 payoff 的期望」可由解一條 PDE 得到。同時可一句連到讀者已熟的 **Black–Scholes PDE ≅ 熱方程**（讀者明確點名要這個層次的對應）。
  - **affine generator → 指數-仿射 MGF ≅ 二次作用量的 Gaussian 路徑積分**：線性 drift + 二次擴散（generator 對 V 至多二階、係數對 V 線性）⇒ 對數 MGF 對狀態變數至多二次（這裡甚至線性 in V_t），正如二次作用量的路徑積分給出 Gaussian/指數型結果。**load-bearing**：這是「為什麼 affine 模型可解」的物理敘事，呼應 narrative spine。
  - **Riccati ODE ≅ Hamilton–Jacobi / 線性化**：D 的 Riccati 可化為線性二階方程，正如 HJ 方程經 Cole–Hopf/對數變換線性化。點出「Riccati 出現＝背後有線性結構＝可解」。**load-bearing**：解釋封閉解存在的結構原因。
- **(f)** 這是全篇最該「慢」的地方：Feynman–Kac、affine ansatz、Riccati 三步都 derive。MGF 最終表達式 (A6/11) 照抄（已驗證），但 D 的 Riccati 解法**示範一次**。

#### 4.4 合體：一維實積分定價公式（eq 8–9 / A7–A10）
- **(c)**
  - eq (A8)：`F(t,T)=E^{\mathbb Q}[VIX_T|\mathcal F_t]=\int_0^\infty p^{\mathbb Q}(V_T|V_t)\sqrt{aV_T+b}\,dV_T\times100`（密度路線）。
  - eq (A7)=eq (8)（密度的 Fourier 反演，供 Fig 4 用）:
    `p^{\mathbb Q}(V_T|V_t)=\frac{1}{\pi}\int_0^\infty \mathrm{Re}\big[e^{-i\phi V_T}f(i\phi;t,\tau,V_t)\big]d\phi`；
    論文 eq(8) 是以 `VIX_T` 為變數的版本：
    `p^{\mathbb Q}(VIX_T|VIX_t)=\frac{2VIX_T}{a\pi}\int_0^\infty \mathrm{Re}\Big[e^{-i\phi\frac{VIX_T^2-b}{a}}f\big(i\phi;t,\tau,(VIX_t^2-b)/a\big)\Big]d\phi`。
  - **eq (9)=eq (A10)（標題成果，一維實積分）**:
    `F(t,T,VIX_t)=\frac{1}{2\sqrt\pi}\int_0^\infty \frac{1-e^{-sb}\,f\!\big(-sa;\,t,\tau,\,(VIX_t^2-b)/a\big)}{s^{3/2}}\,ds`
    （注意：論文 eq(9) 把分子寫成 `1 - e^{-sb} f(-sa;·)`，分母 `√(s^3)=s^{3/2}`，積分變數 **s**，乘上 ×100 的尺度後即 futures 報價。）
- **(d)** 把 4.2 的 `E[√x]=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-E[e^{-sx}]}{s^{3/2}}ds` 與 4.3 的 `E[e^{-s(aV_T+b)}]=e^{-sb}f(-sa;·)` **直接代入合體**，得到 eq(9)。強調三件「so what」：
  - (i) **一維、實值、光滑**被積函數——**完全避開複數 Fourier 反演**（對照 Zhu–Zhang 2007 留下二維複積分、Kahl–Jäckel 2005 的多值對數踩雷）。
  - (ii) `V_t` 由 eq(5) 反解（footnote 2），故公式是 **VIX→futures 的一對一顯式映射**。
  - (iii) eq(8)（密度）vs eq(9)（直接定價）兩條路要講清楚分工：eq(9) 定價用、eq(8) 在 §5 實證畫 steady-state density（Fig 4）用。
- **(e)** 無新物理 box（合體是代數步驟；物理已在 4.2/4.3 給足）。
- **(f)** 合體步驟全展示。**再次提醒**覆蓋前言 `.tex` 舊草稿的錯誤中間式。

### 第 5 節：term-structure 極限與「精確尺」的兩個用途
> 對應論文 eq(12) + §2.3 convexity + §3 實證。建議拆兩 subsection。

#### 5.1 term structure 的飽和：F 隨到期變不敏感（eq 12）
- **(c)** eq (12)：`\lim_{(T-t)\to\infty}F(t,T)=\text{Constant}`（與 spot VIX 無關）。
- **(b/d)** 從 a→0（`κ^Qτ̄` 固定，但 D(φ,τ)、C、A 隨 τ→∞ 趨穩態）說明長天期 futures 收斂到由長期參數決定的常數。**對照**：商品/股票 futures 與現貨一對一連動，VIX futures 不是——這是 mean reversion 的直接後果。**這裡可放一個極短物理 note**：對應 OU/CIR 的**穩態分布**（forward 趨穩態均值），與 5.0 的 Langevin box 呼應；非新類比，故只一句。
- **(f)** 點出與 Fig 2、Fig 3（上升且 concave 的 term structure）、Fig 4（steady-state density）的實證一致。

#### 5.2 用途一：量出 convexity 近似的誤差（§2.3, Fig 1）
- **(a)** 〈為什麼 Taylor-展開平方根會出大錯：convexity 近似的解剖〉
- **(c)** eq (13)（Lin 2007 / Brockhaus–Long 2000 的二階 convexity 修正）:
  `F(t,T)=E^{\mathbb Q}[VIX_T|\mathcal F_t]\approx \sqrt{E_t^{\mathbb Q}[VIX_T^2]} - \frac{\mathrm{var}^{\mathbb Q}(VIX_T^2)}{8\,[E^{\mathbb Q}(VIX_T^2)]^{3/2}}`。
- **(b/d)** 講清楚 (13) 就是**對 √ 在 E[VIX²] 附近做二階泰勒**（讀者懂 Jensen，**不要**講 Jensen 入門；直接講「二階泰勒留一個 −convexity 項」）。Zhang–Shu–Brenner (2010) 推到**三階**。Zhu 的批評（Fig 1 的證據）：
  - Fig 1 參數（Brenner–Shu–Zhang 2007）：`κ=5.5805, θ=0.03259, σ_V=0.5885, √V_0=8.7%`，jump 參數全設 0（純 SV）。
  - 1 年期 futures：**exact = 16.90**，二階近似 = 16.66（相對誤差 **−1.8%**，Lin 一律低估），三階（Zhang et al.）甚至反向**高估**、某些區域比二階更差。
  - 核心結論：**近似精度對 σ_V（vol-of-vol）極敏感**，σ_V 超過約 0.5 後二階/三階都失準；「提高泰勒階數不保證更準」。對照 Little–Pant (2001)：variance swap 誤差 >0.5% 即「fairly large」。
- **(e)** **不放物理 box**（這節是數值/近似誤差分析，無乾淨物理對應；硬放違反讀者原則）。
- **(f)** 這是「為何需要 exact solution」的賣點，**深度中等**：講清泰勒階數與 σ_V 的關係即可，不必重算 Fig 1。

#### 5.3 用途二：MCMC 實證 — 四個嵌套模型，加 jump 值不值（§3）
- **(a)** 〈四個模型的實證對決：SV / SVJ / SVVJ / SVJJ〉
- **(c)** eq (14)（time-discretization，供 MCMC）:
  ```
  Y_t = μ + √V_{t-1} ε_t^S + Z_t^S dq
  V_t = V_{t-1} + κ(θ - V_{t-1}) + σ_V√V_{t-1} ε_t^V + Z_t^V dq
  VIX_t^2 = (a V_t + b)\times100^2 + ε_t^{VIX}
  ```
  `ε^S,ε^V` 標準常態、相關 ρ；`Y_t=ln(S_t/S_{t-1})`；`dq=1` 表跳躍到達；`ε_t^{VIX}` 為 pricing error（Johannes–Polson 2002，獨立零均值常態、已知變異 σ_U²）。
- **(b)** 四個嵌套模型定義：
  - **SV**（Heston，λ=0，無跳）
  - **SVJ**（僅價格跳 Z^S）
  - **SVVJ**（僅變異數跳 Z^V）
  - **SVJJ**（價格與變異數同時跳，最一般）。
- **(d)** 不需推導，但要交代 method：
  - **資料窗**：S&P500 + VIX 日資料 + VIX futures settle，**2004/3/26 – 2008/7/11**；VIX 指數本身回溯到 1990（Fig 2）。過濾規則（<5 天到期、open interest <200、價格 <0.5 剔除）；2007/3/26 前 VXB=VIX×10 的 rescale（除以 10）。最終 **6,433 筆 futures 報價**。
  - **MCMC / WinBUGS（Gibbs sampler）**，理由：optimization calibration 不穩、MCMC 取樣性質優、可同時用 time-series + cross-section 資訊。priors 沿用 Eraker et al. (2003)/Eraker (2004)。
  - **主要發現**（要忠實轉述，避免過度簡化）：
    - Table II 估出的 θ → 年化長期波動 SV/SVJ/SVVJ/SVJJ = 21.1%/20.6%/20.2%/19.7%；加 vol-jump 後 θ 變小（vol jump 吃掉一部分無條件變異）。
    - Table IV（RMSE/MPE/MAE）：以 RMSE、MAE 看 **SVJJ 最佳**（短天期除外）；但 MPE 看 **SV 反而整體最好**，SVVJ 嚴重高估長天期（MPE 高達 10.79%）。
    - **核心結論（呼應摘要）**：把 jump 加到**標的價格**能改善 VIX futures 定價；把 jump 加到**波動率過程幾乎沒幫助**。Heston SV 已是 VIX futures 定價的好候選。
    - 所有模型對短天期都比長天期準（MPE 從 ~3.3% 升到 ~8.9%）。
    - Table III：與 Lin (2007)、Eraker (2004)、Zhang–Zhu (2006) 等的參數比較，η_V（vol risk premium）跨研究差異大；ρ 對 VIX futures 定價不重要（VIX 與 VIX futures 與 ρ 無關）。
- **(e)** **不放物理 box**（純計量/實證）。
- **(f)** 這節**深度偏低**（讀者重心在推導與架構，非計量細節）：MCMC 只交代「為何用、用什麼資料、結論是什麼」，**不展開** Gibbs sampling 內部。Table II/IV 給精選數字即可。

### 第 6 節：結論與 takeaways
- **(b)** 收束三根樑；重申 "umbrella" 解（一式涵蓋 SV/SVJ/SVVJ/SVJJ）、一維實積分的計算優勢、affine 可解性的統一主題。給實務者的 so-what：何時可放心用 Heston、何時近似會咬人（σ_V 大時）、jump 該加在哪。
- **(e)** 可用一句把 narrative spine 的「affine→指數 MGF→Riccati→二次作用量可積分」物理主題收尾（呼應，不新增）。

---

## 3. 推導鏈顯式版（eq1 → eq5 → 積分公式 → MGF/Riccati）

> 寫作者與讀者可據此核對「整條鏈是對的」。標 ★ 處為論文略過、講義需補的步驟；標 ⚠ 為 subtle/易錯。

**Step 0（接口）** model-free
`VIX_t^2 = -\frac{2}{\bar\tau}E^{\mathbb Q}[\ln(S_{t+\bar\tau}/F)]\times100^2`  — eq(2)，`F=S_te^{r\bar\tau}`。
★ 講義補：eq(1)→eq(2) 的 log-contract static replication 骨架（深度見 Q1）。

**Step 1（塌縮成 affine）** 在 eq(4) 下顯式算期望 →
`VIX_t^2=(aV_t+b)\times100^2`  — eq(5)，
`a=\frac{1-e^{-κ^{\mathbb Q}\bar\tau}}{κ^{\mathbb Q}\bar\tau}`，`b=(θ^{\mathbb Q}+\frac{λμ_V}{κ^{\mathbb Q}})(1-a)+λc`，`c=2[\bar\mu^{\mathbb Q}-(μ_S^Q+ρ_Jμ_V)]`  — eq(6)。
★ 講義補：(i) `d\ln S` 的 Itô（含跳）；(ii) `E^{\mathbb Q}[V_u|\mathcal F_t]=θ^{\mathbb Q}+(V_t-θ^{\mathbb Q})e^{-κ^{\mathbb Q}(u-t)}`（由 `dm/du=κ^{\mathbb Q}(θ^{\mathbb Q}-m)` 解）；(iii) 對 u 積分生出 a；(iv) 跳躍期望組裝 b、c。
⚠ b、c 的跳躍項論文直接報結果（引 Lin 2007 / Duan–Yeh 2007），需補中間期望。

**Step 2（鞅 → 期望）**
`F(t,T)=E^{\mathbb Q}[\sqrt{aV_T+b}\,]\times100`  — eq(7)。難點：√ 非多項式 + V_T 密度無封閉式。

**Step 3（Schürger 鑰匙）**
`\sqrt{x}=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-e^{-sx}}{s^{3/2}}ds`  — eq(A9)。
⚠⚠ **讀者記憶中的 `√x=(2/√π)∫e^{-xu²}du` 是錯的**（那給 1/√x）。Zhu 用 Schürger (2002) Laplace/Gamma 型。★ 講義補一行 Gamma/分部積分自證：`\int_0^\infty\frac{1-e^{-sx}}{s^{3/2}}ds=2\sqrt\pi\,\sqrt x`。
取期望 + ★ **Fubini** →
`E[\sqrt{aV_T+b}]=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-e^{-sb}E[e^{-saV_T}]}{s^{3/2}}ds`。

**Step 4（affine MGF 鑰匙）**
`f(\phi;t,\tau,V_t)=E^{\mathbb Q}[e^{\phi V_T}]=e^{C+DV_t+A}`  — eq(10)。
由 Feynman–Kac → backward PIDE eq(A2)；affine ansatz eq(A3) 代入、按 V 冪次比對 → 三 ODE eq(A4)：
`D_\tau=-κ^{\mathbb Q}D+\tfrac12σ_V^2D^2`（Riccati），`C_\tau=κ^{\mathbb Q}θD`，`A_\tau=λE[e^{DZ^V}-1]`；初值 (A5)。
解得 eq(A6)=eq(11)。
★ 講義補：FK 推 PIDE、ansatz 分離、Riccati 解法示範。⚠ `E[e^{DZ^V}]=(1-μ_VD)^{-1}` 需 `μ_VD<1`；λ=0 時 A≡0 退回 Heston。

**Step 5（合體 → 標題成果）** 把 Step 3、4 拼起，`E[e^{-saV_T}]=f(-sa;·)`，`V_t=(VIX_t^2-b)/a`：
`F(t,T,VIX_t)=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-e^{-sb}f(-sa;t,\tau,(VIX_t^2-b)/a)}{s^{3/2}}ds`  — eq(9)=eq(A10)（×100 為報價）。
一維、實值、光滑；避開複數 Fourier 反演。
（密度版 eq(8)/(A7) 供 Fig 4 steady-state density 用。）

---

## 4. 物理對應表（只列真實、標準、load-bearing 的）

| # | 出現章節 | 數學陳述（精確） | 物理對應 | 為何 load-bearing（非裝飾） |
|---|---------|----------------|---------|---------------------------|
| P1 | §2 (CIR) | `dV=κ(θ-V)dt+σ_V\sqrt V\,dW` | **Ornstein–Uhlenbeck / Langevin（乘性噪聲版）**；線性回復力 −κ(V−θ)，穩態為 Gamma（OU 為 Gaussian） | 線性 drift 是後面 affine MGF 可解的根；mean reversion = 回復力直覺 |
| P2 | §4.3 | `E[e^{\phi V_T}]` 滿足 backward PIDE (A2) | **Feynman–Kac ≅ 虛時間 Schrödinger / heat propagator（路徑積分）**；`f`=transform 後的轉移核。連到讀者已熟的 **BS PDE ≅ 熱方程** | 直接解釋「指數型 payoff 的期望」為何 = 解一條 PDE；是 MGF 引擎的物理敘事 |
| P3 | §4.3 / 結論 | generator 對 V 至多二階且係數線性 ⇒ `ln f` 對狀態至多二次（此處線性 in V_t） | **affine MGF ≅ 二次作用量的 Gaussian 路徑積分** | 「為什麼 affine 可解」的統一主題；貫穿 narrative spine |
| P4 | §4.3 | `D_\tau=-κ^{\mathbb Q}D+\tfrac12σ_V^2D^2` 可線性化 | **Riccati ≅ Hamilton–Jacobi（Cole–Hopf/對數變換線性化）** | Riccati 出現＝背後有線性結構＝封閉解存在的結構原因 |
| P5 | §4.2 | `\sqrt x=\frac{1}{2\sqrt\pi}\int_0^\infty\frac{1-e^{-sx}}{s^{3/2}}ds` | **Laplace/Frullani 表示 ≅ 用指數模態（heat-kernel）疊加非多項式可觀測量** | 把 hard nonlinearity √ 拆成「只需 MGF」；繞開複數 Fourier 反演的關鍵 |
| P6 | §5.1（極短） | `\lim_{τ\to\infty}F=`常數 | OU/CIR 的**穩態**（forward 趨穩態均值） | 一句呼應 P1，非新類比；解釋 VIX futures 不與現貨一對一連動 |

**明令避免（do NOT use）**：
- ❌ 「市場=一鍋氣體、股價=氣體分子位置、VIX=溫度、futures=測未來氣溫」——讀者點名動怒，**整個 macro-gas 類比全禁**。
- ❌ 任何把 volatility 直接叫「溫度」、把 mean reversion 比成「天氣回到季節平均」的 cutesy 比喻（`learning-notes.md` line 22–52 即此類，勿沿用）。
- ❌ 「VIX 高＝市場混亂/恐慌」「futures＝對未來的猜測」等入門金融比喻。
- ❌ 為 §2.3 convexity、§3 MCMC 硬塞物理（無乾淨對應 → 不放）。
- ❌ Jensen 不等式的入門級鋪陳（讀者已懂；convexity 修正直接講二階泰勒項即可）。

---

## 5. 明確「不要寫」清單（金融基礎，一律省略）

- VIX 是什麼、怎麼讀數字、為何叫恐慌指數。
- futures 是什麼、futures 的用途（hedging / speculation）。
- option 是什麼、OTM/ITM、put-call。
- no-arbitrage 原理、risk-neutral pricing 的「為什麼」、測度的入門概念（**但 P→Q 對 SVJJ 各參數的具體變換要寫**，那是模型內容不是基礎）。
- 鞅是什麼（**但「futures price 是 Q-鞅 ⇒ F=E^Q[VIX_T]」這個接口要點一句**）。
- term structure 的入門定義、contango/backwardation 基本概念（**但 VIX futures term structure 的飽和特性 eq(12) 要寫**，那是結果）。
- Jensen 不等式入門、convexity 的入門直覺（直接用二階泰勒語言）。
- Brownian motion 是什麼（假設已知；**但 Itô lemma、quadratic variation 要從微積分底子搭起**）。
- Monte Carlo 是什麼、Bayesian/Gibbs 入門（MCMC 只講「為何選它、資料、結論」）。

---

## 6. 給讀者的開放問題（深度/取捨需你拍板）

- **Q1（eq1→eq2 的深度）**：log-contract 的 static replication（Carr–Madan / Breeden–Litzenberger）要展開到什麼程度？選項：(A) 只給「eq(1) 是 eq(2) 的離散化、二階泰勒/權重 1/K²」一段話；(B) 給完整的 `payoff = 線性項 + ∫ put/call dK` 複製證明。你是衍生品實務者，(A) 可能剛好，但你也說要完整推導鏈——傾向哪個？
- **Q2（Riccati 解法的細節）**：D 的 Riccati 要 (A) 完整示範「指數代換化線性二階」解到底，還是 (B) 給「代入 A6 驗證 D_τ 與初值」的驗證級？C、A 是否一律只驗證？我預設 D 完整、C/A 驗證——可接受？
- **Q3（物理 box 密度）**：§4.3 我排了 P2、P3、P4 三個物理 box 連在一起，會不會太密？是否要合併成「一個 affine 可解性的物理段」？你要「真對應」但也忌諱堆砌——這裡是判斷邊界。
- **Q4（書目 discrepancy 的處理）**：封面 31(6),547–571 vs 內頁 32(2),166–190 不一致（見開頭 flag）。講義引用要用哪一組，還是兩組並列加註？
- **Q5（實證深度）**：§5.3 我刻意壓低 MCMC 細節（你重心在推導）。但 Table II–IV、Fig 3–4 要放幾個數字/圖？要不要保留「加 jump 在哪值不值」的完整四模型對比表，還是只留結論句？
- **Q6（Schürger 自證的嚴謹度）**：A9 的自證我建議給「一行 Gamma/分部積分」。要不要更嚴謹地處理 `s→0` 與 `s→∞` 的收斂性（被積函數在 0 附近 `~x/√s`、在 ∞ `~1/s^{3/2}`，均可積）？對物理品味的讀者，補一句收斂性可能加分。
