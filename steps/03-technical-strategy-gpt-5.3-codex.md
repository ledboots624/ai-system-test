外部Web取得が権限制約で使えなかったため、手元の既存調査ログから根拠URLを抽出して、technical-strategy視点で再構成します。

# Technical Strategy for AI-Assisted System Testing in Agentic Coding（Agentic Coding環境におけるAI活用システムテスト戦略）

## Findings（主要な発見）

**Claim（主張）**: AI時代のシステムテストは「実装検証中心」から「仕様整合・接合部リスク検証中心」へ重心移動し、特に統合テスト設計の価値が相対的に上がる。  
**Evidence（根拠）**: 先行調査では、AI生成コードは単体で成立しても結合時に不整合が顕在化しやすく、Mirror-Image（コードとテストの同時生成バイアス）で欠陥検出力が低下しうる。  
**Source（情報源）**:  
- [industry_report/tier3] [The Test Pyramid 2.0: AI-assisted testing across the pyramid](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2025.1695965/full) (2026-03-19) — primary_source: false  
- [academic_paper/tier2] [CodeAssert: Multi-Provider LLM Evaluation for Automated Unit Test Generation](https://recipp.ipp.pt/bitstreams/8ae46aca-ac55-44f8-a98f-6dd4c508463f/download) (2026-03-19) — primary_source: false  
**Confidence（確信度）**: medium

---

## Test Pyramid Re-Architecture（テストピラミッド再設計）

**Claim（主張）**: AI支援開発では、推奨形は「Unit最適化 + Integration厚め + E2E最小十分」の“Diamond寄りピラミッド”。  
**Evidence（根拠）**: AIが生成しやすいUnitは量産可能だが、品質差分は契約・依存・状態連携で発生しやすい。E2Eは最終保証として維持しつつ、過多を避けるのが保守性に有利。  
**Source（情報源）**:  
- [industry_report/tier3] [The Test Pyramid 2.0: AI-assisted testing across the pyramid](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2025.1695965/full) (2026-03-19) — primary_source: false  
- [industry_report/tier2] [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html) (2026-03-19) — primary_source: false  
**Confidence（確信度）**: medium

### Recommended mix（推奨比率・未確定/推測）
- **Unit**: 35–45%（仕様不変条件・純粋ロジック中心）
- **Integration/Contract**: 40–50%（API契約、DB境界、キュー/外部連携）
- **E2E/System**: 10–20%（主要ユーザージャーニー + 高リスク失敗経路）

**Source（情報源）**: [unknown/tier4] [unknown](unknown) (2026-03-19) — primary_source: false  
**Confidence（確信度）**: low（未確定/推測）

---

## CI/CD Integration Blueprint（CI/CDへのAIテスト生成組み込み）

**Claim（主張）**: AIテスト生成は「PR前生成」ではなく「PR内の統制された再現可能ジョブ」として実行し、生成物差分・実行結果・承認ログを残すべき。  
**Evidence（根拠）**: SSDF/SLSA/NIST AI RMFはいずれも、開発プロセスの再現性・トレーサビリティ・リスク管理を重視。GitHub Actionsはテスト自動実行基盤として標準化しやすい。  
**Source（情報源）**:  
- [official_document/tier1] [NIST SP 800-218 Secure Software Development Framework (SSDF)](https://csrc.nist.gov/pubs/sp/800/218/final) (2026-03-19) — primary_source: true  
- [official_document/tier1] [SLSA v1.0 Requirements](https://slsa.dev/spec/v1.0/requirements) (2026-03-19) — primary_source: true  
- [official_document/tier1] [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) (2026-03-19) — primary_source: true  
- [official_document/tier1] [GitHub Actions: Building and testing Node.js](https://docs.github.com/en/actions/use-cases-and-examples/building-and-testing/building-and-testing-nodejs) (2026-03-19) — primary_source: true  
**Confidence（確信度）**: high

### Reference workflow（実装イメージ）

```yaml
name: ai-test-strategy
on: [pull_request]

jobs:
  test-design:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate test charter from spec/diff
        run: ./scripts/ai_generate_test_charter.sh
      - name: Validate charter schema
        run: ./scripts/validate_test_charter.sh

  quality-gates:
    needs: test-design
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run test:unit
      - run: npm run test:integration
      - run: npm run test:e2e:smoke
      - run: npm run test:mutation -- --threshold 60
      - run: npm run sbom && npm run dep:verify
```

### Gate policy（推奨ゲート）
- `MUST`: 失敗テスト0、重大脆弱性0、契約テスト全緑
- `SHOULD`: 変異スコア閾値（例 60→70→80）
- `MAY`: AI提案テストの自動マージ（低リスク領域のみ）

---

## Defensive Test Patterns for AI-Generated Code（AI生成コード向け防御的テストパターン）

**Claim（主張）**: AI生成コードには「幻覚依存」「境界ロジック欠陥」「副作用見落とし」が集中し、防御的テストは“仕様基準 + 契約基準 + 変異/性質基準”の3層が有効。  
**Evidence（根拠）**: 幻覚・不正確依存・Mirror-Image問題は複数報告があり、単純カバレッジでは検出力不足。  
**Source（情報源）**:  
- [academic_paper/tier2] [A Systematic Literature Review of Code Hallucinations in LLMs](https://arxiv.org/abs/2511.00776) (2026-03-19) — primary_source: false  
- [industry_report/tier3] [20% of AI-Generated Code Dependencies Don't Exist](https://www.traxtech.com/blog/20-of-ai-generated-code-dependencies-dont-exist-creating-supply-chain-security-risks) (2026-03-19) — primary_source: false  
- [industry_report/tier3] [Mastering LLM-Generated Tests with Claude Code](https://apimagic.ai/blog/claude/mastering-llm-generated-tests) (2026-03-19) — primary_source: false  
**Confidence（確信度）**: medium（単一/非一次情報を含む）

### Pattern set（優先導入順）
1. **Mirror-Image防止**: テストは“仕様のみ”を入力に生成（コード非参照）。  
2. **Contract-first統合テスト**: OpenAPI/AsyncAPI/DB schemaを真実源に。  
3. **Boundary hardening**: Off-by-one専用テーブルを必須化。  
4. **Property-based / Fuzz**: 想定外入力で副作用検出。  
5. **Mutation testing**: カバレッジの見かけ品質を排除。  
6. **Dependency existence check**: 依存追加PRに実在確認ゲート。

---

## Prompt-Driven Test Design Ops（プロンプト駆動テスト設計運用）

**Claim（主張）**: 高品質化の鍵は「プロンプト標準化」ではなく「プロンプト→生成物→レビュー→実行結果」の閉ループ運用。  
**Evidence（根拠）**: Claude Codeベストプラクティスでも、明示ルール（`CLAUDE.md`）と実行コマンド統制が品質安定化に有効。  
**Source（情報源）**:  
- [official_document/tier1] [Best Practices for Claude Code](https://code.claude.com/docs/en/best-practices) (2026-03-19) — primary_source: true  
**Confidence（確信度）**: high

### `CLAUDE.md` policy skeleton（例）
- `MUST`: 実装前にテスト観点表（同値/境界/状態/決定表）を生成しレビュー承認。  
- `MUST_NOT`: 実装後に期待値を書き換えてテストを通す行為。  
- `SHOULD`: 高リスク変更ではAI生成テスト + 人手追加テストをセット提出。  
- `MAY`: 低リスク定型変更はAI生成Unitを自動提案。

---

## Standards & Requirement Mapping（規格・要件レベル対応）

| Strategy item | Requirement level | Standard reference | Mapping |
|---|---|---|---|
| CIで再現可能なテスト実行・証跡保存 | MUST | NIST SP 800-218 (PW.7, RV.1系), SLSA v1.0 | direct |
| 依存関係検証（実在性・改ざん対策） | MUST | SLSA v1.0, OWASP ASVS（サプライチェーン関連統制） | partial |
| AI生成物のリスク評価・監視 | SHOULD | NIST AI RMF (Govern/Measure/Manage) | direct |
| 変異テスト閾値の段階引き上げ | WANT | [unknown] 実務推奨（未標準） | gap |
| 自動修復E2Eの全面採用 | MAY | [unknown] ツール依存実装指針 | gap |

**Nuance（解釈差）**: `SHOULD`はNIST系では強推奨だが、法令文脈（例: EU規制）では実質`MUST`相当に近づく場合があるため、社内規程ではリスク区分連動で`MUST化`条件を定義すべき。  
**Source（情報源）**:  
- [official_document/tier1] [NIST SP 800-218](https://csrc.nist.gov/pubs/sp/800/218/final) (2026-03-19) — primary_source: true  
- [official_document/tier1] [SLSA v1.0 Requirements](https://slsa.dev/spec/v1.0/requirements) (2026-03-19) — primary_source: true  
- [official_document/tier1] [OWASP ASVS Project](https://owasp.org/www-project-application-security-verification-standard/) (2026-03-19) — primary_source: true  
- [official_document/tier1] [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) (2026-03-19) — primary_source: true  
**Confidence（確信度）**: high（一部マッピングはpartial/gap）

---

## Operational Workflow Example（具体的自動化ワークフロー）

**Claim（主張）**: 実務導入は「段階的強化（30/60/90日）」が最も失敗率が低い。  
**Evidence（根拠）**: 一気通貫導入は、開発速度と品質統制の衝突を起こしやすく、運用定着が難しい（実務知見、未確定）。  
**Source（情報源）**: [unknown/tier4] [unknown](unknown) (2026-03-19) — primary_source: false  
**Confidence（確信度）**: low（推測）

### 30/60/90
- **Day 0–30 (Baseline)**: `CLAUDE.md`整備、統合テスト拡充、依存検証ゲート導入。  
- **Day 31–60 (Hardening)**: Contract test自動生成、Mutation導入、失敗パターン分類。  
- **Day 61–90 (Optimization)**: リスクベース回帰選択、AI観点生成の評価指標化、KPI運用。

### KPI（推奨）
- 統合テスト起因の本番障害率  
- 変更あたりの回帰漏れ率  
- Mutation score  
- AI生成テスト採用率（採用/破棄理由付き）  
- 依存追加PRの差し戻し率

---

## Conclusions & Recommendations（結論と提言）

**Claim（主張）**: 上級者組織では、AI導入の勝ち筋は「テスト自動生成量」ではなく「統合面の検出力」と「統制可能性」。  
**Evidence（根拠）**: AI時代の失敗は実装不足より検証設計不足で発生しやすく、規格系（SSDF/SLSA/NIST）も再現性・証跡・リスク管理を要求。  
**Source（情報源）**:  
- [official_document/tier1] [NIST SP 800-218](https://csrc.nist.gov/pubs/sp/800/218/final) (2026-03-19) — primary_source: true  
- [official_document/tier1] [SLSA v1.0 Requirements](https://slsa.dev/spec/v1.0/requirements) (2026-03-19) — primary_source: true  
- [official_document/tier1] [Best Practices for Claude Code](https://code.claude.com/docs/en/best-practices) (2026-03-19) — primary_source: true  
**Confidence（確信度）**: high

**Priority actions（優先実行）**  
1. 統合テスト比率を先行的に引き上げる（まず +10〜15pt）。  
2. PRゲートに「依存実在確認」「契約テスト」「Mutation最小閾値」を追加。  
3. `CLAUDE.md`でMirror-Image防止を明文化。  
4. 90日でKPI可視化し、E2E増加ではなく“統合検出力”最適化へ継続改善。
