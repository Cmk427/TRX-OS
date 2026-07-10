# TRX Trading OS
## REPORT_TEMPLATE.md

```text
Document ID      : TRX-TPL-003
Document Name    : Report Template
Version          : 1.0.0
Status           : Active
Classification   : Template
Dependencies     : OUTPUT_CONTRACT.md
                   VERIFICATION_POLICY.md
                   DATA_SOURCE_POLICY.md
                   DECISION_TEMPLATE.md
                   DECISION_SNAPSHOT_POLICY.md
                   FAILURE_TAXONOMY.md
Applies To       : Every final report (State 19)
```

---

## 1. 用途

本模板是 `system/OUTPUT_CONTRACT.md` 的直接填空版本，供 State 19（最終報告）
使用。每份完成的分析——包括 `NO TRADE`、`WATCH`、`HOLD`、`REDUCE`、`EXIT`——
均須使用本模板產出。不適用的章節必須標示「不適用」並附原因，不可略去。

若 State 03 或 04 未能完成，改用下方「A. 關鍵驗證中止」格式，不使用完整報告
結構。

複製本檔案作為每次分析的報告草稿，不要直接修改本模板。

---

## A. 關鍵驗證中止（僅在資料無法驗證時使用）

```text
狀態：INSUFFICIENT VERIFIED INFORMATION
Failure Taxonomy 分類：DATA_FAILURE

未能驗證的關鍵項目：
來源／時效狀態：
對決策的影響：
恢復分析所需的確切資訊：
```

---

## A2. 系統覆核要求（僅在修正循環超過上限時使用）

```text
狀態：SYSTEM REVIEW REQUIRED
Failure Taxonomy 分類：CONFLICT_FAILURE

觸發此結果的 State（依 STATE_MACHINE.md §6A 修正循環計數，已達上限 2 次）：
每次修正嘗試的內容：
未能收斂的原因：
建議人類覆核的重點：
```

不得以 `NO TRADE` 取代此狀態——誠實呈現「系統未能收斂」，而非把它包裝成一個
保守的結論。

---

## B. 完整決策報告

### 1. 執行摘要

```text
結果（EXECUTE / WATCH / HOLD / REDUCE / EXIT / NO TRADE）：
信心水平（Very High / High / Medium / Low / Very Low，由 Weighted Composite
  Score 計算，見第 12 節；信心與結果分開判定，滿分通過所有閘門仍可為 Low）：
不確定性等級（Uncertainty：Low / Medium / High / Critical，附具體原因；
  與信心為不同軸線，兩者可以不一致——見第 12 節）：
市場制度（Regime）：
最高優先行動：
最大投資組合風險：
是否允許承擔新風險：
```

### 2. 證據狀態、時點與 Decision Snapshot

```text
市場時段：
資料來源／時效摘要：
重要事實 V1–V5 標籤：
資料衝突（如有）：
關鍵未知事項：

Decision Snapshot（依 DECISION_SNAPSHOT_POLICY.md §2）：
  Decision ID（每個候選／持倉獨立，不同於 Run ID）：
  市場資料時戳：
  各引擎文件版本（Market/Portfolio/Risk/Scanner/Playbook/Options/Decision/
    Committee/Red Team/Master Decision/Execution）：
  Confidence Model 版本（MASTER_DECISION_ENGINE.md §7）：
  Playbook Library 版本：
  修正循環計數（STATE_MACHINE.md §6A）：
  Human Action（發布後補填）：EXECUTED AS PLANNED / EXECUTED DIFFERENTLY /
    DECLINED / NO ACTION YET / HUMAN OVERRIDE（見 CONSTITUTION.md §6A）
  Outcome（後續 Position Review 或 Journal 補填，append-only）：
```

### 3. 市場制度

```text
Market Score（0–100）：
趨勢：
市場寬度：
波動性：
宏觀事件：
板塊輪動：
Regime Transition（§15A）：是否偵測到區間變化（與前一交易日相比）；
  若是，本次為 Transition Period，建議曝險降一級，且既有 Playbook
  指派須於 State 10 重新驗證
建議曝險：
信心：
```

### 4. 投資組合健康

```text
Portfolio Health Score（0–100）：
現金比例：
可用購買力：
最大持倉：
板塊曝險：
Portfolio Construction 檢查（§9A）：單一個股 ≤10%？__　板塊 ≤30%？__
  主題 ≤40%？__（門檻隨帳戶風險偏好調整，任何例外須明確記錄）
相關性風險：
組合限制：
```

### 5. 既有持倉檢視

逐一持倉重複：

```text
股票代號：
狀態：
健康度／趨勢：
風險：
行動（HOLD / REDUCE / EXIT / WATCH）：
原因：
```

### 6. 新機會（最多三個）

逐一候選重複：

```text
股票代號：
Candidate / Opportunity Score：
已驗證催化劑狀態：
入場區間：
目標：
失效條件（Invalidation）：
持有期：
重大未知事項：
```

若無合格候選，標示「不適用 — 無合格候選」。

### 7. Playbook 分類

```text
Playbook 名稱／編號：
Match Score：
理據：
預期行為：
規則參照：
```

### 8. 期權分析

`不適用`（附原因）或：

```text
合約（履約價／到期日）：
已驗證定價：
Greeks：
IV Rank：
流動性：
Option Quality Score：
最大風險：
```

### 9. 風險評估

```text
Risk Score（依 RISK_ENGINE.md §24A 扣分模型計算，非估計值）：
最大組合損失：
倉位大小：
報酬／風險比：
回撤影響：
風險預算佔用：
組合熱度 Heat（依 §7A 公式：Σ 各倉位止損最大損失 ÷ 組合總值；
  期權倉位使用 Premium at Risk，見 RISK_ENGINE.md §28A）：
否決（Veto，如有；Risk Engine 持 Constraint Authority，非 Publication Authority）：
```

### 10. Red Team 覆核

每個必要攻擊類別（`RED_TEAM_ENGINE.md` §4A）均須有明確結論；缺漏視為未經
攻擊：

| 類別 | 發現 | 結果（存活／失敗） |
|---|---|---|
| THESIS ATTACK | | |
| DATA ATTACK | | |
| ASSUMPTION ATTACK | | |
| VALUATION ATTACK | | |
| RISK ATTACK | | |
| TIMING ATTACK | | |
| EXECUTION ATTACK | | |
| Macro / Technical / Portfolio / Catalyst（專項，§4A） | | |

```text
反方論點（Counter-thesis）：
反證（Counter-evidence）：
主要風險：
失敗情境：
替代方案（含現金）：
原論點韌性：
建議調整：
```

### 11. 投資委員會決策

投票選項固定為 STRONG BUY／BUY／WATCH／PASS／REDUCE／REJECT（`COMMITTEE_ENGINE.md`
§5），不可簡化為 Buy/Hold/Avoid：

| 角色 | 權重 | 投票 | 分數（§5A） | 信心 | 支持證據 | 疑慮 |
|---|---|---|---|---|---|---|
| 首席市場策略師 | 25% | | | | | |
| 風控經理 | 25% | | | | | |
| 投資組合經理 | 20% | | | | | |
| 技術分析師 | 15% | | | | | |
| 執行專家 | 10% | | | | | |
| 期權專家 | 5% | | | | | |

```text
Consensus Score（§10 公式：Σ 權重 × 分數）：
共識等級：Unanimous(95+) / Strong(90+) / Moderate(80+) / Weak(70+) / No Consensus(<70)
異議（逐字保留）：
風控經理投 REDUCE 或 REJECT？：是／否
  若是 → 強制升級至 Risk Engine 正式評估（§12）；此為升級機制，非否決權
  （否決權僅屬於 Risk Engine 本身）
```

### 12. Master Decision（唯一最終結果，Publication Authority）

```text
最終結果（EXECUTE / WATCH / HOLD / REDUCE / EXIT / NO TRADE /
  INSUFFICIENT VERIFIED INFORMATION / SYSTEM REVIEW REQUIRED）：
理據：
條件（如有）：
Weighted Composite Score（MASTER_DECISION_ENGINE.md §7，僅決定信心，不決定結果）：
信心：
不確定性等級（§14A，與信心為不同軸線）：Low / Medium / High / Critical — 原因：
已遵守的約束閘門：驗證 ✅/❌　風險 ✅/❌　Red Team ✅/❌
```

Master Decision Engine 不持否決權；上列任一閘門為 ❌ 時，最終結果必須是該閘門
要求的結果，不可由 Master Decision 覆寫（`CONSTITUTION.md` §4A）。

### 13. 執行計劃

`不適用`（附原因）或：

Execution Gate（§1A）— 以下六項須全數通過方可標示為 `READY`：

```text
1. 報價時效性通過？：
2. 流動性通過（≤ 日均量 10% 或未平倉量 10%）？：
3. 價差通過（訂單類型符合 §3A 分級）？：
4. 市場狀態通過（時段已確認、無未確認停牌）？：
5. 訂單規模已核對（與 Risk Engine 核准倉位一致，非私自調整）？：
6. 已向人類呈現核准請求（非自我核准）？：
```

```text
行動／合約：
訂單類型（依 EXECUTION_ENGINE.md §3A 價差／流動性分級表決定）：
入場區間（含時間戳記）：
止損／失效條件：
目標／分批出場邏輯：
倉位大小／所需資金：
最大損失：
時間範圍／覆核日：
流動性檢查（§3B：≤ 日均量 10% 或 ≤ 未平倉量 10%）：
滑價假設（§3C：半價差 + 波動緩衝）：
假定市場時段／停牌狀態（§3D）：
期權專用：買賣價差、IV、未平倉量、距到期日緩衝（§3E）：
人手起飛前檢查：　[ ] 已檢視即時下單畫面　[ ] 已檢視帳戶限制
```

若關鍵執行資料已過時：`DO NOT EXECUTE — REVERIFY`
（分類為 Failure Taxonomy 的 `EXECUTION_INVALID`）。

### 14. 自我審核

- [ ] 所有重要事實均標示來源與時效狀態
- [ ] 無虛構數值或隱藏假設
- [ ] 投資組合檢視先於機會掃描完成
- [ ] 有行動建議時，倉位大小、風險、止損、失效條件與出場均已定義
- [ ] Committee、Red Team 與 Risk 結果均已呈現（含異議）
- [ ] Master Decision 未推翻驗證、風險或 Red Team 的否決
- [ ] Decision Snapshot 各欄位已填妥（第 2 節）；若有缺漏已在此標記為缺陷
- [ ] 信心與不確定性分開呈現，未混為一談
- [ ] Red Team 每個必要攻擊類別均有明確結論（第 10 節），無缺漏類別
- [ ] Committee 投票使用固定六選項，Consensus Score 已依公式計算（第 11 節）

---

## 2. 信心與用語提醒

信心是對證據品質與清晰度的陳述，不是對未來機率的預測。信心由 Weighted
Composite Score 計算（`MASTER_DECISION_ENGINE.md` §7），與「結果」完全分開判
定——結果只由第 12 節的約束閘門決定。因此「所有閘門皆通過的 EXECUTE」仍可能
是 Low Confidence，這是預期中的組合，不是矛盾，必須如實呈現，不可為了與結果
「配對好看」而調高信心。報告不得承諾回報、不得暗示系統可自動執行交易，且必須
保留 `NO TRADE` 作為有效結果的可能性。

---

End of Document

TRX Trading OS v1.0
