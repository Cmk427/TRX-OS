# TRX Trading OS
## examples/EXECUTE_LONG_CALL_EXAMPLE.md

```text
Document ID      : TRX-EX-002
Document Name    : Worked Example — EXECUTE Outcome (Long Call)
Version          : 1.8.0
Status           : Illustrative — fictional data
Classification   : Example
Dependencies     : templates/REPORT_TEMPLATE.md
                   system/OUTPUT_CONTRACT.md
                   engines/OPTIONS_ENGINE.md
                   engines/RISK_ENGINE.md
                   engines/COMMITTEE_ENGINE.md
                   engines/RED_TEAM_ENGINE.md
                   engines/MASTER_DECISION_ENGINE.md
                   playbooks/PLAYBOOK_LIBRARY.md
                   system/DECISION_SNAPSHOT_POLICY.md
                   system/FAILURE_TAXONOMY.md
                   system/INVESTMENT_POLICY.md
Applies To       : Demonstration only
```

---

**這是一個虛構示範，用以展示一個完整走到 `EXECUTE` 結果、並包含 Long Call
比較的分析報告結構，並反映系統目前完整版本的規則（Committee 六選項投票、
Consensus Score 公式、Red Team 攻擊類別、Confidence／Uncertainty 分離、
Decision Snapshot、Execution Gate）。「ACME」為虛構代號，所有價格、Greeks、
IV、財報日期與組合數值均為說明用途而編造，並非任何真實證券的真實資料，亦不
構成交易建議。執行計劃中的所有數字僅示範欄位格式，人類交易者仍須在下單前
重新核實所有即時數據。**

**本範例僅供說明，不具規範效力。所有規則的唯一權威來源是 `system/` 與
`engines/` 目錄下的文件；若本範例與任何引擎文件的規則有任何出入，一律以
引擎文件為準，不得反過來以本範例修正或延伸引擎規則。**

**Specification Versions Targeted**（一眼確認本範例跟隨哪個版本；詳細
各引擎版本見下方 Decision Snapshot 一節）：

- TRX OS：v1.0（完整 Release Version Matrix 見 `docs/ARCHITECTURE.md`）
- Confidence Model：`MASTER_DECISION_ENGINE.md` §7, v1.4.0
- Playbook Library：v1.4.0（PB-003 Post Earnings Continuation）
- Snapshot Policy：`DECISION_SNAPSHOT_POLICY.md` v1.3.0
- 本文件自身版本：見上方標頭 `Version` 欄位

---

## 1. 執行摘要

```text
結果：EXECUTE
信心水平：Very High（WCS 92，見第 12 節計算式；本次現有證據均為 V1 已驗證，
  故信心不因未來未知事件而調低——見下方不確定性欄位分開處理）
不確定性等級：Medium — 原因：下週次要就業數據對成長股情緒的影響尚未可知，
  屬於已知但未解決的短期總經變數。Confidence 與 Uncertainty 為不同軸線
  （MASTER_DECISION_ENGINE.md §7）：現有證據品質高故信心維持 Very High，
  但情境本身仍有未解決的未來變數故不確定性為 Medium——兩者並存屬合理組合，
  不得為了「表達尚有未知」而反過來調低信心
市場制度：Strong（Market Score 84）
最高優先行動：於 ACME 建立 Long Call 研究倉位，規模符合風險預算
最大投資組合風險：本筆交易之最大損失為已付權利金，佔組合 1.2%
是否允許承擔新風險：是 — 市場、組合與風險閘門均通過
```

---

## 2. 證據狀態、時點與 Decision Snapshot

```text
市場時段：Regular（示範時間戳記 2026-07-10 14:35 UTC）
資料來源／時效摘要：
  - ACME 現價／報價：S2，5 分鐘內
  - ACME 期權鏈（履約價、Greeks、IV、未平倉量）：S2，5 分鐘內
  - ACME 財報後上調財測公告：S1（發行人公告），發布於示範日前二日
重要事實 V1–V5 標籤：
  - ACME 現價 128.40：V1
  - ACME 財測上調事件：V1（已由發行人公告證實）
  - IV Rank 38：V1（來源期權鏈計算值）
  - Opportunity Score 91：V3（計算值，見第 6 節權重）
資料衝突：無

Decision Snapshot（依 DECISION_SNAPSHOT_POLICY.md §2）：
  Decision ID：DEC-EX002-ACME-001
  Run ID／時戳：RUN-EX002 / 2026-07-10T14:35:00Z
  市場資料時戳：2026-07-10T14:35:00Z
  各引擎文件版本：Market 1.3.0 / Portfolio 1.3.0 / Risk 1.3.0 / Scanner 1.0.0 /
    Playbook 1.1.0 / Options 1.4.0 / Decision 1.2.0 / Committee 1.4.0 /
    Red Team 1.3.0 / Master Decision 1.4.0 / Execution 1.2.0
  Confidence Model 版本：MASTER_DECISION_ENGINE.md §7, v1.4.0
  Playbook Library 版本：1.3.0（PB-003 Post Earnings Continuation）
  Prompt 版本：NOT APPLICABLE — v1.0 文件驅動分析
  Model 識別碼：NOT APPLICABLE — v1.0 文件驅動分析
  修正循環計數：0（無 State 間反覆修正）
  Human Action：NO ACTION YET（示範文件，未有真實人類覆核）
  Outcome：待後續 Position Review 補填（append-only）

關鍵未知事項：後續一週總體經濟數據公布前，市場對成長股的反應仍有不確定性
```

---

## 3. 市場制度

```text
Market Score（0–100）：84（Strong）
趨勢：主要指數處於上升趨勢，站上 50 日均線
市場寬度：上漲家數比例高於 65%
波動性：VIX 位於近三個月低檔
宏觀事件：本週無重大央行決議；下週有次要就業數據（示範用途）
板塊輪動：資金持續流入半導體與科技成長股
Regime Transition（§15A）：未偵測到——Market Score 已連續兩個交易日處於
  Strong（80+）帶區間，非 Transition Period，曝險無需額外降級
建議曝險：80+ 帶區間 — 建議風險乘數 75%
信心：High
```

---

## 4. 投資組合健康

```text
Portfolio Health Score（0–100）：88（Healthy）
現金比例：31%
可用購買力：等值於總組合的 40%
最大持倉：ETF-CORE，佔組合 22%
板塊曝險：科技股合計 26%，未達集中度上限
Portfolio Construction 檢查（§9A，門檻定義於 `INVESTMENT_POLICY.md` §2）：
  單一個股是否在限額內？是（ACME 執行後預估 4%）
  板塊是否在限額內？是（科技類執行後預估 29%，接近但未突破上限，Portfolio
  Manager 已於 Committee 標註為後續觀察重點）
  主題是否在限額內？不適用（ACME 未歸類於現有追蹤主題）
相關性風險：ACME 與現有持倉相關性低於 0.3，屬分散配置
組合限制：無否決性限制，惟科技板塊集中度執行後將接近 §9A／`INVESTMENT_POLICY.md`
  §2 之板塊上限，後續新增科技類部位須先減碼
```

---

## 5. 既有持倉檢視

```text
股票代號：ETF-CORE（示範持倉）
狀態：Healthy
健康度／趨勢：穩定上升，符合原始論點
風險：低，維持既定停損
原始 Decision Snapshot 記錄之失效條件：跌破 200 日均線且伴隨板塊輪動轉向
失效條件是否觸發：否
行動：HOLD
原因：無新證據推翻原始論點，維持現狀

股票代號：BOND-DEF（示範持倉）
狀態：Healthy
健康度／趨勢：符合防禦性配置目的
風險：低
原始 Decision Snapshot 記錄之失效條件：組合現金水位低於 15%
失效條件是否觸發：否
行動：HOLD
原因：作為組合避險部位，功能未變
```

---

## 6. 新機會（最多三個）

```text
股票代號：ACME
Candidate Quality Score（Scanner，State 09）：88（Qualified）
Opportunity Score（Decision Engine，State 12）：91（Very Strong）
已驗證催化劑狀態：V1 — 發行人公告財測上調
入場區間：127.80 – 129.20（示範區間，需於下單前重新核實）
目標：140.00（第一目標），突破後以移動停利延伸
失效條件（Invalidation）：日線收盤跌破 124.50，或財測公告出現澄清／撤回
持有期：預估 5–10 個交易日（動量延續假設）
重大未知事項：下週次要就業數據對成長股情緒的影響尚未可知
```

---

## 7. Playbook 分類

```text
Playbook 名稱／編號：PB-003 — Post Earnings Continuation
Match Score：92（Excellent）
理據：已驗證財測上調事件、市場制度為 Strong、股價於公告後維持在支撐之上，
      符合 PB-003 的情境與觸發條件
預期行為：延續性上漲，伴隨成交量確認
規則參照：playbooks/PLAYBOOK_LIBRARY.md — PB-003 情境、觸發、失效與風險規則
Playbook 生命週期狀態（§5）：Active
```

---

## 8. 期權分析

```text
標的候選分數門檻檢查：Candidate Quality Score 88 ≥ 80，符合 Options Engine
  前提（依 OPTIONS_ENGINE.md §4，此門檻使用 Scanner 之 Candidate Quality
  Score，非 Decision Engine 之 Opportunity Score——後者於 State 12 才產出，
  Options Review 為 State 11，尚未有該分數可用）
合約（履約價／到期日）：135 Call，35 天後到期（示範合約）
已驗證定價：權利金 4.20（示範值，S2 期權鏈）
Greeks：Delta 0.42，Theta -0.06，Vega 0.18（示範值）
IV Rank：38（中性偏低，非過熱狀態）
流動性：未平倉量 3,200 口，買賣價差 0.05（流動性良好）
Option Quality Score（0–100）：83（Qualified）

named risk factors（§5A）：
  Theta decay：符合預期持有期，非過度衰減
  IV crush 風險：無——持有期間無已知排定之 IV 敏感事件
  Gamma risk：中性，未達近到期或極度價平之高 gamma 區間
  Expiration risk：見下方 Time Risk Rules
  Liquidity/spread risk：良好，價差 0.05 屬正常範圍
  Assignment risk：不適用——本倉位為買入 Long Call，assignment 風險屬賣方，
    不適用於買方

Time Risk Rules（§8A）：
  Min DTE ≥ 30 於入場時？：是（35 DTE）
  預期持有 ≤ 60% of DTE（≤ 21 天）？：是（預估 5–10 個交易日）
  Theta 損失覆核觸發（累計達權利金 20%）？：尚未觸發（入場時點）
  Exit-or-rejustify（DTE ≤ 5）？：不適用（尚有 35 天）

最大風險：已付權利金全額，即每口合約 420 美元（示範值；Premium at Risk，
  非現股停損距離，見 RISK_ENGINE.md §28A）
```

Long Call 相對現股的理由：以有限的已付權利金取得方向性曝險，同時保留現股
配置額度供其他機會使用；下行風險鎖定於權利金，不會產生現股停損滑價風險。
Options Engine 僅測量並回報上述風險，是否可接受由 Risk Engine 判定
（`OPTIONS_ENGINE.md` §8 Prohibited Behaviour）。

---

## 9. 風險評估

```text
Risk Score（依 RISK_ENGINE.md §24A 兩層模型計算）：Layer 1 Hard Reject
  Flags 均未觸發（R/R 2.8:1 高於 2:1 門檻、無 §26/§26A 缺失項目、Heat
  為 Normal 非 Critical、無 Extreme event risk）；Layer 2 扣分項目亦
  均未觸發（停損為結構性支撐位 124.50，非任意百分比；無高相關性、高
  事件風險、流動性不佳或回撤模式）→ 100（Minimal Risk）
最大組合損失：本筆 Long Call 之已付權利金總額，佔組合 1.2%
倉位大小：5 口合約（示範值），符合單一倉位風險預算上限
報酬／風險比：約 2.8 : 1（以第一目標與權利金全損計算）
回撤影響：即使全損，對組合整體回撤影響低於 1.5%
風險預算佔用：科技板塊風險預算使用至 55%，仍有餘裕
組合熱度 Heat（依 §7A 公式：Σ 各倉位止損最大損失 ÷ 組合總值；本倉位使用
  Premium at Risk，見 §28A）：28%，Normal 帶區間（§7：[20%, 40%)），
  執行後仍在可接受範圍
否決（Veto，如有；Risk Engine 持 Constraint Authority，非 Publication
  Authority）：無 — Preliminary 與 Final Risk Gate 均通過
```

---

## 10. Red Team 覆核

每個必要攻擊類別均有明確結論（`RED_TEAM_ENGINE.md` §4A）；11 項全數列出，
無合併省略：

| 類別 | 發現 | 結果 |
|---|---|---|
| THESIS ATTACK | 財測上調後的動量延續論點本身可能只是短線反應，非結構性轉強 | 存活——已有連續兩日支撐確認，非單日反應 |
| DATA ATTACK | 財測上調公告（S1）與期權鏈（S2）均為高層級來源，重新核對後無異常 | 存活 |
| ASSUMPTION ATTACK | 隱含假設「市場對財測上調的正面反應將持續」未必成立 | 存活但保留——假設無法完全消除，已反映於 Medium 不確定性等級，非失敗 |
| MACRO（專項） | 下週次要就業數據若優於預期，可能推升利率預期並打壓成長股估值 | 存活——已列為關鍵未知事項並反映於不確定性等級，非結構性否定原論點 |
| TECHNICAL（專項） | 財報後延續型態是否僅為單日反應，非真正技術面確認 | 存活——已有連續兩日站穩支撐，符合 PB-003 觸發條件 |
| PORTFOLIO（專項） | 本倉位是否會使科技板塊集中度不可逆地突破 §9A 上限 | 存活但有條件——已於 Master Decision 條件中要求執行後暫緩新增科技部位 |
| RISK ATTACK | 風險是否被低估——經檢視 Premium at Risk 模型與 §28A 橋接，未發現低估 | 存活 |
| CATALYST（專項） | 財測上調是否可能已被市場充分定價，追高風險是否因此被低估 | 部分失敗——與 VALUATION ATTACK 發現相同疑慮，已建議提前失效確認時點 |
| VALUATION ATTACK | 入場價是否已反映大部分利多，追高風險存在 | 部分失敗——已建議提前失效條件確認時點以降低此風險 |
| TIMING ATTACK | 現在是否為最佳進場時機，或應等待拉回 | 存活但有保留——已於建議調整中納入分批進場替代方案，非結構性否定 |
| EXECUTION ATTACK | 流動性與價差是否支持此規模執行 | 存活——OI 3,200、價差 0.05，執行可行 |

```text
Resilience Score（`RED_TEAM_ENGINE.md` §19；即 MASTER_DECISION_ENGINE.md
  §7 所稱之 "Red Team Score"，同一分數兩個名稱）：
  失敗類別：CATALYST（專項，−5）、VALUATION ATTACK（基礎，−10）
  100 − 5 − 10 = 85（Acceptable，80–89 帶區間）
```

```text
反方論點：財測上調後的追高風險，以及次要就業數據可能引發的短期逆風
反證：類似財測上調後的延續案例中，仍有相當比例於一週內出現獲利了結拉回
主要風險：若次週就業數據顯著優於預期，可能推升利率預期並打壓成長股估值
失敗情境：股價於公告後三日內未能站穩支撐並跌破 124.50，動量論點失效
替代方案（含現金）：以更小倉位分批進場，或等待拉回至支撐區間再進場
原論點韌性：中高 — 催化劑已由 S1 來源證實，且風險已透過 Long Call 結構限定
建議調整：維持原定倉位規模，但將失效條件提前至跌破 124.50 當日確認，不等收盤
```

---

## 11. 投資委員會決策

投票選項固定為 STRONG BUY／BUY／WATCH／PASS／REDUCE／REJECT
(`COMMITTEE_ENGINE.md` §5)：

| 角色 | 權重 | 投票 | 分數（§5A） | 信心 | 支持證據 | 疑慮 |
|---|---|---|---|---|---|---|
| 首席市場策略師 | 25% | BUY | 80 | High | 市場制度 Strong，板塊輪動有利 | 次週總經數據為短期變數 |
| 風控經理 | 25% | BUY | 80 | Medium | 風險已鎖定於權利金，符合預算 | 建議提前失效條件確認時點；科技板塊集中度接近 §9A 上限 |
| 投資組合經理 | 20% | BUY | 80 | High | 分散度佳，不影響單一個股上限 | 科技板塊集中度執行後接近 30% 上限，需留意後續新增部位 |
| 技術分析師 | 15% | STRONG BUY | 100 | High | 財報後延續型態明確，量能確認 | 追高風險需留意 |
| 執行專家 | 10% | BUY | 80 | Medium | 入場區間明確，流動性足夠分批執行 | 開盤跳空時應重新核價 |
| 期權專家 | 5% | BUY | 80 | High | IV Rank 中性、流動性良好、Greeks 合理 | 到期時間需覆蓋預期持有期 |

```text
Consensus Score（Σ 權重 × 分數）：
  0.25×80 + 0.25×80 + 0.20×80 + 0.15×100 + 0.10×80 + 0.05×80 = 83
共識等級：Moderate Consensus（80+）
異議（逐字保留）：風控經理與投資組合經理均標註科技板塊集中度接近 §9A 上限，
  建議本倉位執行後暫緩任何新增科技類部位；已於「條件」中採納
風控經理投 REDUCE 或 REJECT？：否——本次無強制升級觸發
```

---

## 12. Master Decision（唯一最終結果，Publication Authority）

```text
最終結果：EXECUTE
理據：市場制度 Strong、組合健康且分散、候選具備 V1 級已驗證催化劑、
      Playbook 高度吻合、Options 結構通過品質門檻與 Time Risk Rules、
      風險閘門全數通過，Committee 達 Moderate Consensus 且無否決性異議
條件（如有）：失效條件採用 Red Team 建議之盤中即時確認版本；本倉位執行後
              暫緩任何新增科技類部位（科技板塊集中度已接近 §9A 30% 上限）；
              若下單前價格、IV 或財測狀態已重大改變，須回到 State 04 重新驗證
Weighted Composite Score（MASTER_DECISION_ENGINE.md §7，僅決定信心，不決定
  結果）：
  輸入：Verification Confidence 100（全數 V1）、Risk Score 100（Layer 1/2
    均未觸發）、Market Score 84、Portfolio Health Score 88、Opportunity
    Score 91、Committee(Consensus) Score 83、Red Team(Resilience) Score 85
  WCS = 0.20×100 + 0.20×100 + 0.15×84 + 0.15×88 + 0.15×91 + 0.10×83 + 0.05×85
      = 20 + 20 + 12.6 + 13.2 + 13.65 + 8.3 + 4.25 = 92.0
信心：Very High（WCS 92 落於 90–100 帶區間，§7 WCS-to-Confidence 表）
不確定性等級（§14A，與信心為不同軸線）：Medium — 原因：下週次要就業數據
  結果未知，屬情境本身的未來不確定性，非證據品質問題；不因此調低信心
已遵守的約束閘門：驗證 ✅　風險 ✅　Red Team ✅（無否決，僅提出調整建議）
```

Master Decision Engine 不持否決權；上列任一閘門為 ❌ 時，最終結果必須是該
閘門要求的結果，不可由 Master Decision 覆寫（`CONSTITUTION.md` §4A）。

---

## 13. 執行計劃

Execution Gate（§1A）— 以下六項均已通過，方標示為 `READY`：

```text
1. 報價時效性通過？：是（5 分鐘內）
2. 流動性通過（≤ 日均量 10% 或未平倉量 10%）？：是（5 口合約遠低於未平倉量門檻）
3. 價差通過（訂單類型符合 §3A 分級）？：是（現股價差 <0.10%，屬高流動性；
   期權依規則仍採限價單）
4. 市場狀態通過（時段已確認、無未確認停牌）？：是（Regular session，無停牌）
5. 訂單規模已核對（與 Risk Engine 核准倉位一致）？：是（5 口合約，符合本報告
   第 9 節「風險評估」所載之核准倉位大小——該倉位大小依 RISK_ENGINE.md §6
   Position Sizing 計算，非 RISK_ENGINE.md §9 Drawdown Control；本行所稱
   「第 9 節」指本報告自身章節，非 RISK_ENGINE.md 章節，非事後調整）
6. 已向人類呈現核准請求（非自我核准）？：是（見下方人手起飛前檢查）
```

```text
行動／合約：買入 ACME 135 Call，35 天後到期（示範合約，下單前需重新核實）
訂單類型：限價單，建議不高於權利金 4.30
入場區間（含時間戳記）：127.80–129.20（現股參考價，2026-07-10 14:35 UTC）
止損／失效條件：ACME 盤中價格確認跌破 124.50，或財測公告遭澄清／撤回
目標／分批出場邏輯：現股觸及 140.00 時了結 50%，其餘以移動停利持有
倉位大小／所需資金：5 口合約，預估總成本 2,100 美元（示範值）
最大損失：2,100 美元（權利金全損），佔組合 1.2%
時間範圍／覆核日：持有 5–10 個交易日；到期前至少 5 個交易日強制覆核
  （§8A Time Risk Rules exit-or-rejustify）
流動性／價差備註：買賣價差 0.05，建議避開開盤前 5 分鐘的價差擴大時段
人手起飛前檢查：
  [ ] 已於下單前重新核實現股與期權即時報價
  [ ] 已確認帳戶保證金／權限足以執行本合約
  [ ] 已確認財測公告狀態未被撤回或澄清
  [ ] 已確認執行後不再新增科技類部位（§9A 集中度條件）
```

---

## 14. 自我審核

- [x] 所有重要事實均標示來源與時效狀態（本文件並標明全數為示範虛構資料）
- [x] 無虛構數值當作真實市場資料呈現 — 已於文首與各數值旁標明為示範
- [x] 投資組合檢視先於機會掃描完成
- [x] 倉位大小、風險、止損、失效條件與出場邏輯均已定義
- [x] Committee、Red Team 與 Risk 結果均已呈現，包含風控經理與投資組合經理
      之集中度疑慮
- [x] Master Decision 未推翻驗證、風險或 Red Team 的任何否決（本例無否決）
- [x] Decision Snapshot 各欄位已填妥（第 2 節）
- [x] 信心與不確定性分開呈現，未混為一談
- [x] Red Team 每個必要攻擊類別均有明確結論（第 10 節），無缺漏類別
- [x] Committee 投票使用固定六選項，Consensus Score 已依公式計算（第 11 節）

---

End of Document

TRX Trading OS v1.0
