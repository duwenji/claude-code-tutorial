# 2-3: 組み込みスキル活用

> **学習時間**: 20分 | **難易度**: ⭐⭐

## バンドルスキルとは

**バンドルスキル**は Claude Code に標準搭載されており、インストール直後からすぐに使えるスキルです。組み込みコマンド（`/help`、`/compact` 等）とは異なり、バンドルスキルはプロンプトベースで動作します。Claude が詳細な指示を受け取り、ツールを使って作業を調整します。

> `disableBundledSkills` 設定で全バンドルスキルを無効化できます。

## バンドルスキル一覧

| スキル | コマンド | 用途 |
|-------|---------|------|
| code-review | `/code-review` | コードレビュー（バグ・クリーンアップ検出） |
| simplify | `/simplify` | コード整理・簡略化（バグ検出なし） |
| security-review | `/security-review` | セキュリティレビュー |
| debug | `/debug` | デバッグログの有効化とセッション診断 |
| run | `/run` | アプリ起動・動作確認 |
| verify | `/verify` | コード変更の動作確認 |
| run-skill-generator | `/run-skill-generator` | `/run`・`/verify` 用レシピ生成 |
| batch | `/batch` | コードベース全体への並列大規模変更 |
| loop | `/loop` | 繰り返し実行 |
| claude-api | `/claude-api` | Claude API リファレンス読み込み |
| fewer-permission-prompts | `/fewer-permission-prompts` | 許可プロンプトの削減 |

---

## /code-review

現在のブランチ差分またはファイルをレビューします。

```bash
# ブランチ全体をレビュー
/code-review

# 特定ファイルをレビュー
/code-review src/components/Button.tsx

# レビューして自動修正
/code-review --fix

# PR にインラインコメントを投稿
/code-review --comment

# クラウドでマルチエージェントレビュー
/code-review ultra
```

レビュー観点：
- 正確性（バグ・ロジックエラー）
- 再利用性・簡潔さ
- パフォーマンス
- セキュリティ

**努力レベル指定**：

```bash
/code-review low       # 高確信度の指摘のみ
/code-review high      # 広範なカバレッジ
/code-review ultra     # クラウドでマルチエージェント深層レビュー
```

---

## /simplify

変更されたコードの重複・複雑さ・非効率を検出し、自動改善します（バグ検出は行いません）。

```bash
/simplify

# 特定ファイルまたは PR を対象に
/simplify src/utils.ts
```

4つのサブエージェントが並列で実行します：
- 既存ヘルパーの再利用
- コードの単純化
- 効率化
- 抽象レベルの適正化

> **注意**: バグを探すには `/code-review` を使ってください。`/simplify` は品質向上のみです。

---

## /debug

セッションのデバッグログを有効化して問題を診断します。

```bash
# デバッグログを有効化
/debug

# 問題の説明を渡してフォーカス分析
/debug TypeError: Cannot read properties of undefined at UserList.tsx:23
```

> **重要**: `/debug` は Claude Code 自体のデバッグ診断ツールです。コードのバグ修正を依頼する場合は、直接 Claude に問題を説明してください。

---

## /run

アプリを起動して実際に動作を確認します。

```bash
/run
```

プロジェクトの種類（React, Node.js, Python 等）を自動検出し、適切な起動コマンドを実行します。フロントエンドの変更はブラウザで確認し、スクリーンショットを取得します。

複雑な起動手順（データベース・環境ファイル・マルチステップビルド）がある場合は `/run-skill-generator` で事前にレシピを記録しておくと確実です。

---

## /verify

コードの変更が期待通りに動作するか、実際にアプリを起動して確認します。

```bash
/verify
```

テストやタイプチェックだけでなく、実際の動作で確認します。

---

## /security-review

現在のブランチ差分のセキュリティ上の問題を検出します。

```bash
/security-review
```

チェック項目：
- SQL インジェクション
- XSS（クロスサイトスクリプティング）
- CSRF
- 認証・認可の問題
- 機密情報のハードコード
- 依存パッケージの脆弱性

---

## /code-review ultra（クラウドマルチエージェントレビュー）

複数のサブエージェントがクラウドで並列レビューを行う高精度モードです。`/ultrareview` は現在 `/code-review ultra` のエイリアスになっています：

```bash
/code-review ultra

# GitHub PR 番号を指定
/code-review ultra 42
```

通常の `/code-review` より深い分析が行われますが、時間とトークンを消費します。Pro・Max プランで 3 回まで無料です。

---

## スキルのカスタマイズ

バンドルスキルは上書きできます。`.claude/skills/code-review/SKILL.md` を作成すると、プロジェクト固有の動作に変更できます：

```markdown
---
name: code-review
description: このプロジェクト専用のコードレビュースキル
---

# プロジェクト専用 コードレビュー

## 追加チェック項目
- Tailwind クラスの順序（Prettier プラグインに準拠）
- コンポーネントの最大行数（100行以下）
- テストファイルの同時更新確認

（以下、標準のレビュー観点...）
```

## 🏋️ 練習問題

1. **【確認】** 組み込みスキルとカスタムスキルの違いを説明してください。
2. **【実践】** `/code-review` を実行して、出力の構造を確認しましょう。
3. **【実践】** `/security-review` を任意のファイルに対して実行してみましょう。
4. **【応用】** `/code-review` と `/security-review` を使い分ける基準を考えてみましょう。

## 次のステップ

→ [2-4: カスタムスキル作成](04-custom-skills.md) に進む
