---
marp: true
theme: default
paginate: true
style: |
  @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap');
  section {
    font-family: 'Noto Sans JP', 'Helvetica Neue', sans-serif;
    background: linear-gradient(135deg, #34d399 0%, #059669 100%);
    color: #ffffff;
    padding: 40px 60px;
  }
  section.title {
    background: linear-gradient(135deg, #1a2e1a 0%, #1e3e1e 50%, #0f4f2f 100%);
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  section.title h1 { font-size: 2.4em; color: #e2e8f0; margin-bottom: 0.2em; }
  section.title p { color: #94a3b8; font-size: 1.1em; }
  section.light {
    background: #ffffff;
    color: #1e293b;
  }
  section.light h2 { color: #047857; border-bottom: 3px solid #10b981; padding-bottom: 8px; }
  section.alert {
    background: linear-gradient(135deg, #7f1d1d 0%, #991b1b 100%);
    color: #ffffff;
  }
  section.alert h2 { color: #fecaca; border-bottom: 3px solid #f87171; }
  section.success {
    background: linear-gradient(135deg, #064e3b 0%, #065f46 100%);
    color: #ffffff;
  }
  section.success h2 { color: #a7f3d0; border-bottom: 3px solid #34d399; }
  section.dark {
    background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
    color: #e2e8f0;
  }
  h2 { font-size: 1.6em; margin-bottom: 0.6em; }
  strong { color: #fbbf24; }
  table { font-size: 0.85em; width: 100%; border-collapse: collapse; }
  th { background: rgba(255,255,255,0.2); padding: 8px 12px; text-align: left; }
  td { padding: 8px 12px; border-bottom: 1px solid rgba(255,255,255,0.1); }
  ul { line-height: 1.7; }
  li { margin-bottom: 0.3em; }
  code { background: rgba(0,0,0,0.3); padding: 2px 8px; border-radius: 4px; font-size: 0.9em; }
  .badge { display: inline-block; padding: 2px 10px; border-radius: 12px; font-size: 0.8em; font-weight: bold; }
  .high { background: #c6f6d5; color: #22543d; }
  .medium { background: #fefcbf; color: #744210; }
  .low { background: #fed7d7; color: #742a2a; }
---
<!-- _class: title -->

# テスト工学（学習）：Claude Code時代の<br>AI活用システムテスト技法

AI駆動開発における品質保証の変革と実践的デザインパターン

2026-03-19 | AI Research Agent

---
<!-- _class: light -->

## Executive Summary：AI時代のテスト設計

AIコーディングツール（Claude Code等）の普及により、テストの重点は「実装検証」から「意図と仕様の整合性検証」へ移行しました。

- **役割転換**: テスト技法は「テストケース作成手順」から「AIへの論理的指示言語」へ進化 <span class="badge medium">Medium</span>
- **最大のリスク**: もっともらしい誤り（Plausible Error）とコード/テストの同時生成による共倒れ（Mirror-Image Bias） <span class="badge high">High</span>
- **戦略**: Unitテストの自動量産と、Integrationテストへの人間リソースの集中（Diamond型ピラミッド） <span class="badge medium">Medium</span>
- **成果**: 知識グラフとLLMを組み合わせたPrompt-Driven Test Generationは、障害検出率を約35%向上させる可能性 <span class="badge medium">Medium</span>

---
<!-- _class: light -->

## Context：システムテスト定義の現代的再構築

開発パラダイムの変化に伴い、システムテストの定義と目的が再構築されています。

- **実装の検証から意図の検証へ**
  AIが高速に実装を行うため、人間は「その実装がビジネス意図と合致しているか」の検証に注力する必要があります。<span class="badge high">High</span>
- **AIへの指示書としてのテスト設計**
  古典的なテスト技法（同値分割など）は、AIに対して曖昧さを排除した仕様を伝えるための「共通言語」として機能します。<span class="badge medium">Medium</span>
- **検収基準の厳格化**
  AI生成コードは一見正しく見えるため、ブラックボックステストによる外部振る舞いの厳格な検証がより重要になります。<span class="badge high">High</span>

---
<!-- _class: light -->

## Claude Code開発フローとテストの責務

Agentic Coding（自律型コーディング）環境におけるテストプロセスの変化です。

1. **仕様策定フェーズ**:
   自然言語の仕様を、AIが理解可能な「構造化された論理（デシジョンテーブル等）」に変換する。<span class="badge high">High</span>
2. **実装・単体テストフェーズ**:
   AIがコードと単体テストを生成。人間はレビューと「エッジケースの指摘」に徹する。<span class="badge medium">Medium</span>
3. **システムテストフェーズ**:
   AIが見落としがちな「システム全体の整合性」「非機能要件」「複雑なビジネスルール」を人間主導で検証する。<span class="badge high">High</span>

---
<!-- _class: alert -->

## Critical Risk：AI生成コードの品質課題

AI活用開発において警戒すべき新たな品質リスクです。

- **Plausible Error（もっともらしい誤り）** <span class="badge high">High</span>
  構文的に正しく、ロジックも通りそうだが、ビジネス要件の細部で致命的に間違っているコード。発見が困難。
- **Hallucination（幻覚）** <span class="badge medium">Medium</span>
  存在しないライブラリメソッドの呼び出しや、仕様にない勝手な仕様追加。
- **過度な抽象化と複雑化** <span class="badge medium">Medium</span>
  単純なロジックに対して、AIが不必要に複雑なパターンや過剰な抽象化を適用し、保守性を下げるリスク。

---
<!-- _class: alert -->

## Risk Deep Dive：Mirror-Image Bias

AIにコードとテストの両方を生成させる際に発生する構造的な欠陥です。

- **共倒れ現象** <span class="badge high">High</span>
  実装コードとテストコードを同じコンテキスト（プロンプト）で生成すると、AIは「同じ勘違い」を両方に反映させる傾向があります。
- **検証の無効化**
  間違ったロジックに対して、その間違いをパスする間違ったテストコードが生成され、CIはグリーンになるがバグが残ります。
- **対策**
  実装とテストの生成プロセス（またはAgent）を分離し、独立した仕様からテストを生成させる必要があります。<span class="badge medium">Medium</span>

---
<!-- _class: light -->

## 技法再定義①：同値分割法 (Equivalence Partitioning)

AI時代において、同値分割は「データの偏り検知」ツールとして機能します。

- **AI時代の役割**: **「データ多様性の確保」** <span class="badge medium">Medium</span>
- **活用アプローチ**:
  - 仕様から同値クラス（有効/無効）をAIに抽出させる。
  - AIの学習データバイアスによる「特定パターンの偏り」を人間がレビューで検知する。
  - 「ビジネス的に意味のあるクラスか」を判断することに人間のリソースを集中する。
- **プロンプト効果**: 「同値クラスを抽出して」と指示することで、生成されるテストケースの網羅性が向上します。

---
<!-- _class: light -->

## 技法再定義②：境界値分析 (Boundary Value Analysis)

単純な境界値はAIに任せ、人間は「隠れた境界」を探します。

- **AI時代の役割**: **「決定境界の探索」** <span class="badge medium">Medium</span>
- **AIの得意領域**:
  - 数値的な境界（<, <=, >, >=）の網羅的なテストケース生成。
  - 基本的なオフ・バイ・ワン（Off-by-one）エラーの検出。
- **人間の担当領域**:
  - **Hidden Boundaries**: 仕様書にはないが実装上存在する内部的な閾値（バッファサイズ、タイムアウト等）。
  - **非線形な挙動**: AIモデルの予測精度が急激に変化する境界や、RAGの検索閾値の検証。

---
<!-- _class: light -->

## 技法再定義③：デシジョンテーブル (Decision Table)

AIへの最も強力な「仕様指示書」として機能します。

- **AI時代の役割**: **「ロジックの制約定義」** <span class="badge high">High</span>
- **なぜ有効か**:
  - 自然言語の仕様書は曖昧さを含みやすく、AIのハルシネーション（勝手な解釈）を招く原因となる。
  - 表形式（Y/N/Action）の論理定義は解釈の余地が狭く、厳密なコード生成を強制できる。
- **制限**:
  - 状態爆発に注意。あくまで「確定的なビジネスルール」の検証に使用し、確率的なAI挙動の検証には不向き。<span class="badge medium">Medium</span>

---
<!-- _class: light -->

## 技法再定義④：状態遷移テスト (State Transition)

チャットボットやAgentワークフローの検証における最重要技法です。

- **AI時代の役割**: **「コンテキスト維持の検証」** <span class="badge high">High</span>
- **適用対象**:
  - マルチターン対話を行うAIエージェント。
  - 文脈（Context）によって応答が変わるシステム。
- **AI活用**:
  - AIに「状態遷移図（PlantUML等）」を描かせ、人間が「到達不能状態」や「不正な遷移」がないかレビューするペアプログラミング的アプローチが有効。<span class="badge medium">Medium</span>

---
<!-- _class: light -->

## 技法再定義⑤：ユースケースとシナリオ

「シナリオエクスプロージョン」による網羅性向上を目指します。

- **AI時代の役割**: **「E2Eプロンプト生成の基盤」** <span class="badge medium">Medium</span>
- **Scenario Explosion**:
  - 正常系シナリオだけでなく、悪意ある入力、中断、例外フローなどのエッジケースシナリオをAIに大量生成させる。
  - 人間が思いつかないような複合条件のシナリオをLLMの知識ベースから引き出す。
- **注意点**:
  - 生成されたシナリオが現実的かどうかの取捨選択が必要。

---
<!-- _class: light -->

## プロンプト駆動テスト (PDTG) の実践

Prompt-Driven Test Generation (PDTG) は、テスト設計の新しいパラダイムです。

- **技法名明示のプロンプト** <span class="badge medium">Medium</span>
  「境界値分析を使って」「ペアワイズ法で」と技法名を明示することで、AI生成テストの構造と網羅性が向上します。
- **リスク起点の付記要求**
  単にテストを作らせるのではなく、「セキュリティリスクの高い箇所を重点的に」といったリスクベースの指示が推論品質を高めます。
- **効果**:
  LLMとドメイン知識グラフを組み合わせた手法で、障害検出率が約35%向上した事例あり。<span class="badge medium">Medium</span>

---
<!-- _class: light -->

## テストピラミッドの再構築 (Diamond Model)

AI支援開発では、伝統的なピラミッド型から「ダイヤモンド寄り」へのシフトが推奨されます。

- **Unit (底辺) - 自動化・最適化** <span class="badge medium">Medium</span>
  AIが得意とする領域。量は多いが、契約や依存関係のバグは見逃しやすい。
- **Integration (中間) - 厚くする** <span class="badge high">High</span>
  AI生成コンポーネント同士の接合部、モックと実挙動の乖離など、AI開発で最もバグが多発する層。ここにリソースを集中する。
- **E2E (頂点) - 最小十分** <span class="badge medium">Medium</span>
  保守コストが高いため、AIに頼りすぎず、ビジネス上の最重要フロー（クリティカルパス）の最終保証として維持する。

---
<!-- _class: light -->

## 統合テストの重要性と設計パターン

AIコンポーネントの「接合部」こそが品質の主戦場です。

- **契約テスト (Contract Testing)** <span class="badge high">High</span>
  AIが生成したAPIクライアントと、実際のサーバー仕様の乖離を検知する。AIは古いAPI仕様を学習している可能性があるため必須。
- **依存関係の実在確認** <span class="badge medium">Medium</span>
  AIがインポートしたライブラリや呼び出している関数が、プロジェクト内に実在し、期待通り動作するか検証する。
- **状態連携の検証**
  複数のAIエージェントが連携する場合、データの受け渡しと状態の整合性を重点的にテストする。

---
<!-- _class: light -->

## E2EテストとClaude Codeの連携

UI変更に脆いE2Eテストを、AIでどう維持するか。

- **Self-Healing Tests** <span class="badge low">Low</span>
  UI要素のIDや構造が変わった際に、AIが自動的にセレクタを修復する仕組みの導入（まだ発展途上だが有効）。
- **ユーザーシミュレーション**
  Claude Code等のAIを使用して、実際のユーザー操作に近いランダム性を持ったテスト実行を行う。
- **視覚的回帰テスト (Visual Regression)**
  AI生成コードがCSSを崩していないか、ピクセルレベルではなく「見た目の違和感」をAIに判定させるアプローチ。

---
<!-- _class: light -->

## CI/CDパイプラインへの組み込み

自動化されたパイプラインにAIレビューを組み込みます。

- **AI Code Reviewer** <span class="badge medium">Medium</span>
  PR作成時に、AIエージェントが差分を解析し、「テストが不足している箇所」や「仕様との矛盾」を指摘するボットを導入。
- **失敗時の自動解析**
  CIでテストが落ちた際、ログをAIに解析させ、修正案を提示させるワークフロー。
- **継続的なテスト生成**
  コードベースが更新されるたびに、影響範囲を特定し、必要な回帰テストをAIが提案・生成する。

---
<!-- _class: alert -->

## 批評的視点：AI活用の限界と盲点

AIは万能ではありません。以下の点に注意が必要です。

- **コンテキストの欠落** <span class="badge high">High</span>
  AIはプロジェクトの全歴史や暗黙知を知りません。「なぜそのような仕様になったか」という背景理解なしにコードを変更し、デグレを起こすリスクがあります。
- **創造性の欠如** <span class="badge medium">Medium</span>
  AIは学習データにあるパターンしか提案できません。前例のない革新的なテストシナリオや、人間の直感に基づく探索的テストは代替できません。
- **確信度の過信**
  AIは間違っている時でも自信満々に回答します。出力結果に対する人間による批判的思考（Critical Thinking）が不可欠です。

---
<!-- _class: success -->

## Recommendations：実践的アクション

上級エンジニアが明日から取り組むべきアクションアイテム。

1. **テスト生成プロンプトのテンプレート化** <span class="badge high">High</span>
   「同値分割」「境界値」「デシジョンテーブル」等の技法名を組み込んだプロンプトテンプレートを整備し、チームで共有する。
2. **Integrationテストへのシフト** <span class="badge high">High</span>
   単体テストの数よりも、コンポーネント間・システム間の結合テストの充実度をKPIにする。
3. **仕様の構造化** <span class="badge medium">Medium</span>
   AIへの入力となる仕様書を、自然言語からマークダウンの表やPlantUML等の構造化データに書き換える。

---
<!-- _class: success -->

## 学習ロードマップとチェックリスト

AI時代のテスト工学を習得するためのステップ。

- **Step 1: 基礎の再確認**
  古典的技法（同値分割、境界値、状態遷移）の論理構造を再学習する。
- **Step 2: プロンプトエンジニアリング**
  テスト技法をAIへの指示（プロンプト）に翻訳するスキルを磨く。
- **Step 3: AI特性の理解**
  LLMの弱点（計算ミス、幻覚、コンテキスト長制限）を把握し、そこを補うテストを設計する。
- **Step 4: パイプライン構築**
  Claude Code等のツールをCI/CDに統合し、自動化ループを構築する。

---
<!-- _class: dark -->

## Conclusion

**「テスト工学は、AIを使いこなすための論理言語である」**

AI時代において、テスト設計技法は陳腐化するどころか、AIのポテンシャルを最大限に引き出し、そのリスクを制御するための最も重要なスキルセットとなります。

AIを「単なるコード生成機」にするか、「信頼できる開発パートナー」にするかは、私たちのテスト設計能力にかかっています。

---
<!-- _class: light -->

### Reference
本資料は **AI Research Agent** により生成された調査レポートに基づいています。
- 調査日時: 2026-03-19
- 主要ソース: The Prompt Alchemist (2024), CodeAssert (2026), The Test Pyramid 2.0 (2026)
- 詳細: 社内共有ドキュメント `06-integration-review-claude-opus-4.6.md` を参照

---

<!-- _class: dark -->

## Thank You

AI Research Agent によるリサーチ結果をご覧いただきありがとうございました。

本資料に関するご質問・フィードバックをお待ちしています。
