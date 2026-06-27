# Roadmap

## v0.1.0（現在）— 初版

- [x] README.md
- [x] 全27セクションの初稿
- [x] サンプルスキル3種
- [x] QUICK-REFERENCE.md

## v0.2.0（予定）— コンテンツ拡充・正確性向上

- [ ] 各セクションに実践演習を追加
- [ ] スクリーンショット・デモGIF追加
- [ ] Quiz アプリとの連携
- [ ] 追加サンプルスキル（5種）

### 新規セクション候補（公式ドキュメント調査で確認済み）

- [ ] **Plan モード（`/plan` / Shift+Tab）** — 実装前にアプローチを計画・承認するインタラクティブモード。複雑なタスクでの無駄なトークン消費を防ぐ。公式ドキュメント: `https://code.claude.com/docs/en/permission-modes`
- [ ] **Git Worktrees 統合** — Git ワークツリーを使った並列作業。複数のブランチを同時に Claude に処理させる。公式ドキュメント: `https://code.claude.com/docs/en/worktrees`

## v0.3.0（予定）— 発展トピック

- [ ] Claude API 直接利用との連携
- [ ] カスタムエージェント実装例
- [ ] 大規模プロジェクトでの運用事例

### 新規セクション候補（Part 6 調査で確認済み）

- [ ] **Agent Teams（マルチエージェント）** — `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` で有効化できる複数 Claude インスタンスの並列協調作業。現在は実験的機能。
- [ ] **コード Intelligence プラグイン** — 型定義付き言語向けのシンボルナビゲーションプラグイン。不要なファイル読み込みを減らしてコスト削減。
- [ ] **LLM ゲートウェイ連携** — Amazon Bedrock・Google Vertex AI・Microsoft Foundry を使った企業向けコスト追跡・データ残留管理。
- [ ] **GitHub Code Review 自動化** — `claude-code-action@v1` の `@claude` メンション応答機能と、PR 自動レビュー（毎 PR 実行）の2種類の使い分け。

## アイデア（検討中）

- 動画チュートリアルへの対応
- 英語版の作成
- インタラクティブな演習環境
