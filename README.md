## AI Runtime

All AI systems (Claude, ChatGPT, Gemini, Codex, etc.) should initialize TRX through:

trx/BOOTSTRAP.md

Do not load documents manually.

The bootstrap process defines the required loading order and runtime behavior.

# TRX Trading OS

**Trading Research eXecution Operating System**  
**版本：** v1.0  
**狀態：** 文件架構基礎完成  
**範圍：** 可驗證、可審計、由人類執行的美股、ETF 與 Long Call 交易研究

---

## 使命

TRX Trading OS 是一個制度化的 AI 交易研究操作系統。它協助交易者理解市場環境、檢視組合風險、篩選研究候選、建立可反駁的交易假設，並產出供人類覆核的執行計劃。

TRX 的目的不是增加交易頻率，而是改善決策品質：

> **Better Decisions, Not More Trades.**

現金是有效的投資組合倉位；`NO TRADE` 是有效且常常更好的決策。

---

## 邊界

TRX：

- 是研究及決策支援系統，不是自動交易程式；
- 不會連接券商、傳送訂單或聲稱已執行交易；
- 不會保證回報、虛構市場資料、新聞、催化劑或期權資料；
- 只在資料、風險與組合限制允許時提出研究建議；及
- 始終保留人類交易者對資本配置及訂單執行的唯一權限。

預設研究範圍包括美國股票、ETF、動量 swing trading（通常 1–7 個交易日）及 Long Call 研究。其他期權策略必須先建立獨立規則、資料及風險控制，才可納入系統。

---

## 核心原則

TRX 以以下規則運作：

1. 真實性、驗證及證據優先於結論。
2. 先檢視既有投資組合，再研究新機會。
3. 風險控制優先於預期回報；Risk Engine 可作硬性否決。
4. 每個看法均需反方論點；Red Team 可因重大風險否決。
5. 事實、分析、推論、假設、未知與建議不可混為一談。
6. Master Decision Engine 是唯一可發布最終整合結果的模組，但不得推翻資料、風險或 Red Team 的否決。
7. 「權限」分為兩種且互不競爭：Risk / Red Team / 驗證閘門持有的是**否決性約束權**（Constraint Authority），Master Decision Engine 持有的是**發布權**（Publication Authority）——後者從不能凌駕前者。

完整規範見 [Core Principles](system/CORE_PRINCIPLES.md)、[Constitution](system/CONSTITUTION.md)（§4A 定義兩種權限）及 [System Governance](system/SYSTEM.md)。

---

## 實際架構

```text
TRX-Trading-Research-eXecution--main/
├── README.md
├── system/
│   ├── SYSTEM.md
│   ├── CONSTITUTION.md
│   ├── CORE_PRINCIPLES.md
│   ├── OUTPUT_CONTRACT.md
│   ├── STATE_MACHINE.md
│   ├── VERIFICATION_POLICY.md
│   ├── DATA_SOURCE_POLICY.md
│   ├── DECISION_SNAPSHOT_POLICY.md   # 每次決策鎖定的資料／版本快照，含 Decision History 政策
│   ├── ENGINE_INTERFACE_CONTRACT.md  # 引擎間結構化介面（僅命名/格式，不改權限）
│   ├── FAILURE_TAXONOMY.md           # 非 EXECUTE 結果的分類法
│   ├── PARAMETER_REGISTRY.md         # 數值參數索引（僅供查閱，衝突時以原文件為準）
│   ├── DOCUMENTATION_GOVERNANCE.md   # 新文件標頭標準（Owner/Last Updated），不追溯舊文件
│   └── INVESTMENT_POLICY.md          # 單一持倉／板塊／現金集中度上限與資金優先順序原則的唯一owner
├── engines/
│   ├── MARKET_ENGINE.md
│   ├── PORTFOLIO_ENGINE.md
│   ├── SCANNER_ENGINE.md
│   ├── PLAYBOOK_ENGINE.md
│   ├── OPTIONS_ENGINE.md       # 保留原有 Long Call 研究邏輯
│   ├── RISK_ENGINE.md
│   ├── DECISION_ENGINE.md
│   ├── MASTER_DECISION_ENGINE.md
│   ├── COMMITTEE_ENGINE.md
│   ├── RED_TEAM_ENGINE.md
│   ├── PORTFOLIO_OPTIMIZATION_ENGINE.md  # State 17：已發布結果的目標權重／股數／資金
│   ├── CAPITAL_ALLOCATION_ENGINE.md      # State 18：釋出資金的 Deploy／Wait 決定
│   ├── EXECUTION_ENGINE.md
│   ├── POSITION_MANAGEMENT_ENGINE.md     # 觸發式持倉覆核（無 State，會開新一輪分析）
│   └── PORTFOLIO_REBALANCING_ENGINE.md   # 週期性板塊／主題再平衡（無 State，會開新一輪分析）
├── playbooks/
│   └── PLAYBOOK_LIBRARY.md
├── docs/
│   ├── ARCHITECTURE.md
│   ├── ROADMAP.md
│   ├── RESPONSIBILITY_MATRIX.md
│   ├── DEPENDENCY_MAP.md
│   ├── AI_AGENT_IMPLEMENTATION_GUIDE.md  # 給實作 AI agent 框架的非規範性檢查清單
│   └── PORTFOLIO_MANAGEMENT_GUIDE.md     # 非規範性詞彙表（Hold/Add/Reduce/Exit/Rotate/Optimize/Rebalance）
├── schemas/                 # ENGINE_INTERFACE_CONTRACT.md 的機器可讀 JSON Schema 版本
├── templates/
│   ├── ANALYSIS_TEMPLATE.md
│   ├── DECISION_TEMPLATE.md
│   ├── REPORT_TEMPLATE.md
│   └── PORTFOLIO_PROFILE_TEMPLATE.md     # 選填的人類風格／風險偏好／持有期填空表
├── workflows/
│   └── WORKFLOWS.md        # 尚未定義具體自動化工作流；佔位並列出規則
└── examples/
    ├── NO_TRADE_EXAMPLE.md
    └── EXECUTE_LONG_CALL_EXAMPLE.md
```

`OPTIONS_ENGINE.md` 是為保留既有 Long Call 範圍而保留的必要擴充引擎；`SCANNER_ENGINE.md` 現只負責候選發現及排序。

---

## 唯一工作流

[State Machine](system/STATE_MACHINE.md) 是唯一的流程來源，任何引擎不得自行改寫順序：

```text
輸入 → 驗證資料 → 市場及宏觀分析 → 投資組合檢視
→ 初步風險閘門 → 持倉檢視 → 機會掃描 → Playbook 分類
→ 期權檢視（如適用）→ 排名 → Committee → Red Team
→ 最終風險閘門 → Master Decision → 投資組合優化 → 資金配置
→ 執行計劃（如可行）→ 自我審核 → 最終報告
```

持續的持倉觸發式覆核（獲利／虧損／財報／IV 突增）與週期性板塊再平衡並非上述
流程的延伸步驟——兩者分別定義在 `POSITION_MANAGEMENT_ENGINE.md` 與
`PORTFOLIO_REBALANCING_ENGINE.md`，觸發後會開啟全新一輪分析（重新從頭跑一次
上述流程），而不是在既有分析中插入額外步驟。

如關鍵資料未能驗證，系統以 `INSUFFICIENT VERIFIED INFORMATION` 結束，並說明缺少什麼。若資料完整但市場、組合或風險不支持新部署，系統產出 `NO TRADE`、`WATCH`、`HOLD`、`REDUCE` 或 `EXIT`；這些均是完成的保護性結果。若 Master Decision 與 Risk／Self Audit 之間的修正循環超過上限（最多 2 次全流程修正，見 [State Machine](system/STATE_MACHINE.md) §6A），系統以 `SYSTEM REVIEW REQUIRED` 結束並列出未能收斂的原因——這同樣是完成、可審計的結果，而非靜默卡住。

---

## 模組責任

| 模組 | 主要責任 |
|---|---|
| System / Constitution | 治理、權限、否決層級及人類控制 |
| Verification / Data Source | V1–V5 證據標籤、來源質量、時效及衝突處理 |
| Market / Portfolio | 市場制度、宏觀、持倉、集中度、買入能力及組合相容性 |
| Scanner / Playbook / Options | 候選宇宙、可重複 setup 分類，以及 Long Call 與現股比較 |
| Risk | 倉位、最大損失（可計算的 Portfolio Heat／Risk Score 公式）、回撤、事件與硬性否決（Constraint Authority） |
| Committee / Red Team | 多角色獨立評估、保留異議、反證及替代方案（Red Team 持 Constraint Authority） |
| Decision / Master Decision | 候選排序，以及唯一最終結果整合與報告發布（Publication Authority，不持否決權） |
| Portfolio Optimization | 將已發布結果換算為目標權重、股數、釋出資金；只量化，不重新決定 |
| Capital Allocation | 對已釋出資金決定 Deploy（投入本輪已核准候選）或 Wait；不創造新機會 |
| Execution | 人類覆核的完整 Execution Package（入場、止損、目標、下單類型、有效期、未成交後續處理、流動性／滑價）；不下單 |
| Position Management / Portfolio Rebalancing | 觸發式／週期性覆核，決定何時開啟新一輪分析；本身不發布任何持倉決定 |

詳見 [Architecture](docs/ARCHITECTURE.md)、[Responsibility Matrix](docs/RESPONSIBILITY_MATRIX.md)（誰可以做什麼的速查表）及 [Dependency Map](docs/DEPENDENCY_MAP.md)（文件間依賴關係，避免循環依賴）。

---

## Quick Start

每份完成的決策報告以繁體中文及 Markdown 呈現，結構定義在
[Output Contract](system/OUTPUT_CONTRACT.md)；所有重要事實依
[Verification Policy](system/VERIFICATION_POLICY.md) 標示 V1–V5。

依序使用 [templates/](templates/) 三份填空模板即可完成一次分析：
[ANALYSIS_TEMPLATE.md](templates/ANALYSIS_TEMPLATE.md)（State 02–11）→
[DECISION_TEMPLATE.md](templates/DECISION_TEMPLATE.md)（State 12–20）→
[REPORT_TEMPLATE.md](templates/REPORT_TEMPLATE.md)（State 21 最終報告）。
[examples/](examples/) 提供兩份虛構完整示範可供對照
（`NO_TRADE_EXAMPLE.md`、`EXECUTE_LONG_CALL_EXAMPLE.md`）。

若要接入 AI agent 框架執行本系統，先讀
[AI Agent Implementation Guide](docs/AI_AGENT_IMPLEMENTATION_GUIDE.md)，
再參考 [schemas/](schemas/) 的機器可讀 schema。

---

## Roadmap

v1.0 完成制度化文件架構，不包含實盤交易程式。後續的結構化輸入／報告 schema、研究測試案例、交易日誌及績效覆檢，均須在不削弱驗證、風險否決及人類控制的前提下另行批准。

詳見 [Roadmap](docs/ROADMAP.md)。

---

TRX Trading OS v1.0
