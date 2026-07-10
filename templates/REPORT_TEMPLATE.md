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

未能驗證的關鍵項目：
來源／時效狀態：
對決策的影響：
恢復分析所需的確切資訊：
```

---

## B. 完整決策報告

### 1. 執行摘要

```text
結果（EXECUTE / WATCH / HOLD / REDUCE / EXIT / NO TRADE）：
信心水平（Very High / High / Medium / Low / Very Low）：
市場制度（Regime）：
最高優先行動：
最大投資組合風險：
是否允許承擔新風險：
```

### 2. 證據狀態與時點

```text
市場時段：
資料來源／時效摘要：
重要事實 V1–V5 標籤：
資料衝突（如有）：
關鍵未知事項：
```

### 3. 市場制度

```text
Market Score（0–100）：
趨勢：
市場寬度：
波動性：
宏觀事件：
板塊輪動：
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
Risk Score 定義與數值：
最大組合損失：
倉位大小：
報酬／風險比：
回撤影響：
風險預算佔用：
組合熱度（Heat）：
否決（Veto，如有）：
```

### 10. Red Team 覆核

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

| 角色 | 投票 | 信心 | 支持證據 | 疑慮 |
|---|---|---|---|---|
| 首席市場策略師 | | | | |
| 技術分析師 | | | | |
| 投資組合經理 | | | | |
| 風控經理 | | | | |
| 期權專家 | | | | |
| 執行專家 | | | | |

```text
共識：
異議（逐字保留）：
```

### 12. Master Decision（唯一最終結果）

```text
最終結果：
理據：
條件（如有）：
信心：
已遵守的約束閘門：驗證 ✅/❌　風險 ✅/❌　Red Team ✅/❌
```

### 13. 執行計劃

`不適用`（附原因）或：

```text
行動／合約：
訂單類型：
入場區間（含時間戳記）：
止損／失效條件：
目標／分批出場邏輯：
倉位大小／所需資金：
最大損失：
時間範圍／覆核日：
流動性／價差備註：
人手起飛前檢查：　[ ] 已檢視即時下單畫面　[ ] 已檢視帳戶限制
```

### 14. 自我審核

- [ ] 所有重要事實均標示來源與時效狀態
- [ ] 無虛構數值或隱藏假設
- [ ] 投資組合檢視先於機會掃描完成
- [ ] 有行動建議時，倉位大小、風險、止損、失效條件與出場均已定義
- [ ] Committee、Red Team 與 Risk 結果均已呈現（含異議）
- [ ] Master Decision 未推翻驗證、風險或 Red Team 的否決

---

## 2. 信心與用語提醒

信心是對證據品質與清晰度的陳述，不是對未來機率的預測。報告不得承諾回報、
不得暗示系統可自動執行交易，且必須保留 `NO TRADE` 作為有效結果的可能性。

---

End of Document

TRX Trading OS v1.0
