# TRX Trading OS
## examples/NO_TRADE_EXAMPLE.md

```text
Document ID      : TRX-EX-001
Document Name    : Worked Example — NO TRADE Outcome
Version          : 1.7.0
Status           : Illustrative — fictional data
Classification   : Example
Dependencies     : templates/REPORT_TEMPLATE.md
                   system/OUTPUT_CONTRACT.md
                   engines/RISK_ENGINE.md
                   engines/COMMITTEE_ENGINE.md
                   engines/RED_TEAM_ENGINE.md
                   engines/MASTER_DECISION_ENGINE.md
                   system/DECISION_SNAPSHOT_POLICY.md
                   system/FAILURE_TAXONOMY.md
Applies To       : Demonstration only
```

---

**這是一個虛構示範，用以展示 `templates/REPORT_TEMPLATE.md` 及
`system/OUTPUT_CONTRACT.md` 的完整結構，並反映系統目前完整版本的規則
（Committee 六選項投票、Consensus Score 公式、Red Team 攻擊類別、
Confidence／Uncertainty 分離、Decision Snapshot）。「DEMO」為虛構代號，
所有價格、事件與組合數值均為說明用途而編造，並非任何真實證券的真實資料，
亦不構成交易建議。**

**本範例僅供說明，不具規範效力。所有規則的唯一權威來源是 `system/` 與
`engines/` 目錄下的文件；若本範例與任何引擎文件的規則有任何出入，一律以
引擎文件為準，不得反過來以本範例修正或延伸引擎規則。**

**Specification Versions Targeted**（一眼確認本範例跟隨哪個版本；詳細
各引擎版本見下方 Decision Snapshot 一節）：

- TRX OS：v1.0（完整 Release Version Matrix 見 `docs/ARCHITECTURE.md`）
- Confidence Model：`MASTER_DECISION_ENGINE.md` §7, v1.4.0
- Playbook Library：不適用 — 本次無候選進入 Playbook 分類
- Snapshot Policy：`DECISION_SNAPSHOT_POLICY.md` v1.1.0
- 本文件自身版本：見上方標頭 `Version` 欄位

---

## 1. 執行摘要

```text
結果：NO TRADE
Failure Taxonomy 分類：RISK_REJECTION（Preliminary Risk Gate 之 Layer 1
  Hard Reject Flag 否決任何新科技類曝險，見第 9 節）
信心水平：Very Low（WCS 45，見第 12 節計算式——見下方說明：Very Low
  Confidence 在此並非代表「結果不可靠」，而是如實反映輸入證據與風險
  評分本身確實偏弱；NO TRADE 結果由 Risk／Portfolio 閘門獨立否決，
  不因信心分數高低而改變，這正示範了 §7「信心不決定結果」的分離原則）
不確定性等級：Medium — 原因：DEMO 催化劑尚未由 S1/S2 來源證實，屬證據本身
  未完備。信心與不確定性為不同軸線：本例信心偏低是因為多項輸入分數本身
  就偏弱（Risk Score 0、Opportunity Score 缺失），不確定性則單獨反映
  催化劑真偽未定這件事——兩者成因不同，恰好都指向同一方向，但並非同一件事
市場制度：Weak（Market Score 62）
最高優先行動：維持現金水位，暫緩任何新倉位
最大投資組合風險：現有 TECH 主題曝險集中度偏高
是否允許承擔新風險：否 — Preliminary Risk Gate 已限制新曝險
```

---

## 2. 證據狀態、時點與 Decision Snapshot

```text
市場時段：Regular（示範時間戳記 2026-07-10 21:30 UTC）
資料來源／時效摘要：市場指數與 VIX 數值標示為 S2，10 分鐘內；候選股 DEMO 的
                     催化劑新聞標示為 S3，來源時間為示範日前一日
重要事實 V1–V5 標籤：
  - 大盤 VIX 水平：V2（來源已知，單一來源，未交叉核對）
  - DEMO 「產品發布」催化劑：V3（僅一個未確認來源，視為估計而非事實）
資料衝突：無

Decision Snapshot（依 DECISION_SNAPSHOT_POLICY.md §2）：
  Decision ID：DEC-EX001-TECH-001（既有 TECH 部位 REDUCE 決策）
  Run ID／時戳：RUN-EX001 / 2026-07-10T21:30:00Z
  市場資料時戳：2026-07-10T21:30:00Z
  各引擎文件版本：Market 1.3.0 / Portfolio 1.3.0 / Risk 1.3.0 / Scanner 1.0.0 /
    Playbook NOT APPLICABLE — 無候選進入 State 10 / Options NOT APPLICABLE
    — 未達候選分數門檻 / Decision NOT APPLICABLE — 無候選進入 State 12 /
    Committee 1.4.0 / Red Team 1.3.0 / Master Decision 1.4.0 /
    Execution NOT APPLICABLE — 結果非 EXECUTE/REDUCE(新倉位)/EXIT
  Confidence Model 版本：MASTER_DECISION_ENGINE.md §7, v1.4.0
  Playbook Library 版本：不適用——本次無候選進入 Playbook 分類
  Prompt 版本：NOT APPLICABLE — v1.0 文件驅動分析
  Model 識別碼：NOT APPLICABLE — v1.0 文件驅動分析
  修正循環計數：0
  Human Action：NO ACTION YET（示範文件，未有真實人類覆核）
  Outcome：待後續 Position Review 補填（append-only）

關鍵未知事項：DEMO 催化劑的官方公告尚未由 S1/S2 來源證實
```

---

## 3. 市場制度

```text
Market Score（0–100）：62
趨勢：短期反彈，中期仍在下降通道
市場寬度：多頭股票比例低於 40%
波動性：VIX 偏高，處於近三個月上緣
宏觀事件：三日內有主要央行利率決議（示範用途）
板塊輪動：資金由成長股流向防禦性板塊
Regime Transition（§15A）：偵測到——Market Score 由前一交易日之 Strong（80+）
  帶區間降至本次 Weak（60+）帶區間，本次為 Transition Period，且既有 TECH
  相關之 Playbook 指派須於下次候選分析時重新驗證，不可沿用轉變前的判斷
建議曝險：Market Score 62 原始對應 §16 之「60+」風險乘數帶區間（25%），惟依
  §15A，Transition Period 期間須再降一級至下一個更保守之風險乘數帶區間，
  即「Below 60」帶區間之「No New Position」——本次建議曝險為 0%（不新增
  部位），而非僅降至 25%；此結果與本次 Preliminary Risk Gate 之否決方向
  一致，並非相互獨立的兩個結論
信心：High（多項指標一致指向防禦性環境）
```

---

## 4. 投資組合健康

```text
Portfolio Health Score（0–100）：71（Acceptable）
現金比例：18%
可用購買力：等值於總組合的 22%
最大持倉：TECH 主題 ETF，佔組合 34%
板塊曝險：科技股合計 58%，集中度偏高
Portfolio Construction 檢查（§9A）：單一個股 ≤10%？否（TECH 主題 ETF 佔
  34%，突破單一持倉上限）板塊 ≤30%？否（科技股合計 58%，遠超 30% 上限）
  主題 ≤40%？否（科技主題合計已超過 40% 上限）——三項限制均已突破，這正是
  本次 NO TRADE 與既有部位 REDUCE 的核心成因，而非單純的市場制度轉弱
相關性風險：現有持倉與候選股 DEMO 高度相關（同屬半導體供應鏈主題）
組合限制：科技股曝險已全面突破 §9A 集中度上限，任何新科技類倉位須先減碼
  現有部位；此為 Portfolio Engine 的硬性建構限制，先於 Risk Engine 之風險
  評分獨立成立
```

---

## 5. 既有持倉檢視

```text
股票代號：TECH（示範持倉）
狀態：At Risk
健康度／趨勢：短期轉弱，跌破 20 日均線
風險：與大盤防禦性輪動同向下行
原始 Decision Snapshot 記錄之失效條件：跌破 20 日均線且板塊相對強度轉弱
失效條件是否觸發：是——本次 Position Review 發現已同時符合兩項條件，
  依 PORTFOLIO_ENGINE.md §8A 立即升級至 Risk Engine，不等待下次排定覆核
行動：REDUCE
原因：集中度已達上限，且市場制度轉弱，優先降低現有曝險而非新增部位
```

---

## 6. 新機會（最多三個）

```text
股票代號：DEMO
Candidate Quality Score（Scanner，State 09）：76（Qualified，未進入 Decision
  排名，故無 Opportunity Score）
已驗證催化劑狀態：未驗證（V3，見上方證據狀態）
入場區間：不適用 — 未達分派門檻
目標：不適用
失效條件（Invalidation）：不適用
持有期：不適用
重大未知事項：催化劑真實性、市場制度是否於一週內回穩
```

本次無候選達到 Playbook Engine 85+ 執行門檻，且與現有 §9A 集中度限制衝突，
因此 DEMO 本身未進入 Committee 排名——下方第 11 節之 Committee 決策，評估
的是「本次是否允許任何新風險部署」此一整體問題（含 DEMO 與既有 TECH 部位
處置），而非針對 DEMO 單一候選的買賣表決。

---

## 7. Playbook 分類

```text
Playbook 名稱／編號：不適用 — 無候選通過 Scanner 與 §9A 集中度檢查
Match Score：不適用
理據：DEMO 雖具備動量特徵，但與現有 TECH 集中倉位高度相關，且市場制度為
      Weak，不符合 PB-001 Momentum Breakout 的 Regime 前提
預期行為：不適用
規則參照：playbooks/PLAYBOOK_LIBRARY.md — PB-001 No-trade 條件
```

---

## 8. 期權分析

```text
不適用 — 標的股未通過 Options Engine 的候選分數門檻（需 ≥80，DEMO 為 76）
```

---

## 9. 風險評估

```text
Risk Score（依 RISK_ENGINE.md §24A 兩層模型計算）：Layer 1 Hard Reject
  Flag 已觸發（Critical Portfolio Heat ≥80%，見下方 Heat 數值）→ 0
  （Reject），Layer 2 扣分模型不予計算。Preliminary Risk Gate 因此評估
  為 Restrict，未進入 Final Risk Gate 之完整流程
最大組合損失：不適用 — 無新增倉位
倉位大小：不適用
報酬／風險比：不適用
回撤影響：現有 TECH 部位若不減碼，估計組合回撤敏感度將維持高檔
風險預算佔用：科技板塊已使用 92% 之板塊風險預算
組合熱度 Heat（依 §7A 公式：Σ 各倉位止損最大損失 ÷ 組合總值）：84%，
  Critical 帶區間（§7：[80%, 100%]）——觸發 Risk Score Layer 1 Hard
  Reject Flag
否決（Veto，如有；Risk Engine 持 Constraint Authority，非 Publication
  Authority）：Risk Engine 初步風險閘門否決任何新科技類曝險
```

---

## 10. Red Team 覆核

每個必要攻擊類別均有明確結論（`RED_TEAM_ENGINE.md` §4A）；11 項全數列出，
無合併省略：

| 類別 | 發現 | 結果 |
|---|---|---|
| THESIS ATTACK | 即使 DEMO 具備獨立催化劑，集中度與市場制度風險已足以否決此案 | 失敗——thesis 本身即不成立，無需進一步檢驗 |
| DATA ATTACK | DEMO 催化劑僅 V3（單一未確認來源），未達可信門檻 | 失敗——資料本身不足 |
| ASSUMPTION ATTACK | 假設「市場制度將於短期回穩」缺乏證據支持 | 失敗 |
| MACRO（專項） | 三日內央行決議前的部位調整是否恰當 | 存活——REDUCE 決策與此一致，非額外風險 |
| TECHNICAL（專項） | DEMO 動量指標偏正向是否足以獨立支持進場 | 存活——技術面本身無缺陷，但不足以推翻集中度與制度風險，非結構性否定 |
| PORTFOLIO（專項） | DEMO 與既有 TECH 部位高度相關，是否會進一步放大集中風險 | 失敗——確實會放大集中風險，此為支持 REDUCE／NO TRADE 的關鍵發現之一 |
| RISK ATTACK | 現有 TECH 集中度是否被低估 | 失敗——經檢視後集中度風險甚至高於初步評估 |
| CATALYST（專項） | DEMO 「產品發布」催化劑本身的可信度是否成立 | 失敗——僅 V3 單一未確認來源，可信度未建立 |
| VALUATION ATTACK | 不適用——候選未進入定價分析階段 | 不適用 |
| TIMING ATTACK | 當前市場制度轉弱期間是否仍有例外進場時機 | 失敗——Transition Period 期間應優先降風險 |
| EXECUTION ATTACK | 不適用——無執行計劃 | 不適用 |

```text
Resilience Score（`RED_TEAM_ENGINE.md` §19；即 MASTER_DECISION_ENGINE.md
  §7 所稱之 "Red Team Score"）：
  失敗類別（基礎，各 −10）：THESIS、DATA、ASSUMPTION、RISK、TIMING = −50
  失敗類別（專項，各 −5）：PORTFOLIO、CATALYST = −10
  VALUATION、EXECUTION 標示不適用（`not_applicable`，依 §4A 之窄範圍規則——
    僅此兩類別可標示不適用，且僅限其攻擊對象確實尚未存在時），不計入扣分
  100 − 50 − 10 = 40（Reject，<70 帶區間）——與本次 NO TRADE 結果方向一致
```

```text
反方論點：即使 DEMO 具備獨立催化劑，集中度與市場制度風險已足以否決此案
反證：過去類似的防禦性輪動環境中，同主題突破的失敗率高於平均
主要風險：催化劑未經證實即可能是消息面炒作
失敗情境：若催化劑證實為假，DEMO 及既有 TECH 部位可能同步大幅回落
替代方案（含現金）：維持現金與短天期防禦性配置，待市場制度回升後重新評估
原論點韌性：低 — 過度依賴單一未證實催化劑
建議調整：先執行 TECH 既有部位減碼，暫緩任何新倉位
```

---

## 11. 投資委員會決策

投票選項固定為 STRONG BUY／BUY／WATCH／PASS／REDUCE／REJECT
(`COMMITTEE_ENGINE.md` §5)，本次評估「是否允許任何新風險部署」：

| 角色 | 權重 | 投票 | 分數（§5A） | 信心 | 支持證據 | 疑慮 |
|---|---|---|---|---|---|---|
| 首席市場策略師 | 25% | REJECT | 0 | High | Market Score 62，板塊輪動偏防禦 | 中期趨勢尚未反轉確認 |
| 風控經理 | 25% | REJECT | 0 | High | 初步風險閘門已否決；§9A 三項集中度限制均已突破 | 無 |
| 投資組合經理 | 20% | REJECT | 0 | High | 科技集中度已全面超過 §9A 上限 | 若不減碼，任何新曝險都放大集中風險 |
| 技術分析師 | 15% | PASS | 40 | Medium | DEMO 動量指標偏正向 | 大盤逆風下技術訊號可信度降低 |
| 執行專家 | 10% | PASS | 40 | Medium | 目前無可執行之入場條件 | 若催化劑證實，需重新評估時效 |
| 期權專家 | 5% | PASS | 40 | High | 標的未達 Options 分析門檻 | 不適用 |

```text
Consensus Score（Σ 權重 × 分數）：
  0.25×0 + 0.25×0 + 0.20×0 + 0.15×40 + 0.10×40 + 0.05×40 = 12
共識等級：No Consensus（<70 帶區間），惟本例三項最高權重角色（合計 70%）
  一致投 REJECT，故未落入單純「無共識」情境，而是明確傾向拒絕
異議（逐字保留）：技術分析師與執行專家標註 DEMO 本身技術面尚可，但均同意
  在集中度與風控閘門已否決的前提下，此屬次要考量
風控經理投 REDUCE 或 REJECT？：是（REJECT）→ 依 §12 強制升級至 Risk Engine
  正式評估；Risk Engine 已於第 9 節確認 Preliminary Risk Gate 之否決
```

---

## 12. Master Decision（唯一最終結果，Publication Authority）

```text
最終結果：NO TRADE
理據：市場制度偏弱且處於 Transition Period、既有科技集中度已全面突破 §9A
      三項限制、候選催化劑未經證實，且 Preliminary Risk Gate 已否決任何
      新曝險；Committee 加權傾向 REJECT，風控經理之 REJECT 已升級並經
      Risk Engine 確認
條件（如有）：待 (1) 市場 Score 回升至 70 以上且脫離 Transition Period、
              (2) TECH 集中度降至 §9A 上限以下、(3) DEMO 催化劑取得
              S1/S2 來源證實後，可重新進入 Scanner 階段
Weighted Composite Score（MASTER_DECISION_ENGINE.md §7，僅決定信心，不
  決定結果）：
  輸入：Verification Confidence 75（最差為 V3）、Risk Score 0（Layer 1
    Hard Reject）、Market Score 62、Portfolio Health Score 71、
    Opportunity Score UNKNOWN（DEMO 未進入 Decision 排名，非 gate-linked
    缺值，依 §7 按比例重新分配權重）、Committee(Consensus) Score 12、
    Red Team(Resilience) Score 40
  權重重新分配後（Opportunity 15% 移除，其餘 85% 依比例放大至 100%）：
    Verification 23.5%、Risk 23.5%、Market 17.6%、Portfolio 17.6%、
    Committee 11.8%、Red Team 5.9%
  WCS ≈ 0.235×75 + 0.235×0 + 0.176×62 + 0.176×71 + 0.118×12 + 0.059×40
      ≈ 17.6 + 0 + 10.9 + 12.5 + 1.4 + 2.4 ≈ 45
信心：Very Low（WCS ≈45 落於 <60 帶區間；缺值本應再降一級，惟已處於最低
  帶區間）——如第 1 節所述，這正確反映輸入證據與風險評分本身偏弱，
  NO TRADE 結果由 Risk／Portfolio 閘門獨立決定，不受此信心分數影響
不確定性等級（§14A，與信心為不同軸線）：Medium — 原因：DEMO 催化劑真偽
  未定，屬證據完備度問題，與上述信心計算的成因不同但方向一致
已遵守的約束閘門：驗證 ✅　風險 ✅（否決已保留）　Red Team ✅
```

---

## 13. 執行計劃

```text
不適用 — 本次結果為 NO TRADE，且既有 TECH 部位之 REDUCE 行動另由持有人依其
自身帳戶條件安排執行時機；本報告不提供該筆減碼之下單細節。若持有人執行
該 REDUCE，Execution Gate（§1A）之流動性、價差與人手核准檢查仍適用。
```

---

## 14. 自我審核

- [x] 所有重要事實均標示來源與時效狀態
- [x] 無虛構數值或隱藏假設（本文件標明全數為示範虛構資料）
- [x] 投資組合檢視先於機會掃描完成
- [x] 無新增行動建議，故倉位大小／止損／出場均標示不適用
- [x] Committee、Red Team 與 Risk 結果均已呈現，且異議已記錄
- [x] Master Decision 未推翻驗證、風險或 Red Team 的否決
- [x] Decision Snapshot 各欄位已填妥（第 2 節）
- [x] 信心與不確定性分開呈現，未混為一談
- [x] Red Team 每個必要攻擊類別均有明確結論（第 10 節），不適用項目已標明原因
- [x] Committee 投票使用固定六選項，Consensus Score 已依公式計算（第 11 節）

---

End of Document

TRX Trading OS v1.0
