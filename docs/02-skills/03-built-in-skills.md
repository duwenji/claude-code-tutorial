# 2-3: 組み込みスキル活用

> **学習時間**: 20分 | **難易度**: ⭐⭐

## 組み込みスキル一覧

Claude Code には以下のスキルが標準搭載されています。

| スキル | コマンド | 用途 |
|-------|---------|------|
| code-review | `/code-review` | コードレビュー |
| debug | `/debug` | デバッグ支援 |
| run | `/run` | アプリ起動・動作確認 |
| security-review | `/security-review` | セキュリティレビュー |
| simplify | `/simplify` | コード簡略化 |
| skill-creator | `/skill-creator` | スキル生成 |
| init | `/init` | CLAUDE.md 生成 |

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
```

レビュー観点：
- 正確性（バグ・ロジックエラー）
- 再利用性・簡潔さ
- パフォーマンス
- セキュリティ

---

## /debug

エラーや不具合の原因を特定して修正します。

```bash
# エラーメッセージを貼って実行
/debug

# または自然言語で
> /debug TypeError: Cannot read properties of undefined at UserList.tsx:23
```

デバッグプロセス：
1. エラーメッセージ・スタックトレースを解析
2. 関連ファイルを読み込む
3. 根本原因を特定
4. 修正案を提示・適用

---

## /run

アプリを起動して実際に動作を確認します。

```bash
/run
```

プロジェクトの種類（React, Node.js, Python 等）を自動検出し、適切な起動コマンドを実行します。フロントエンドの変更はブラウザで確認し、スクリーンショットを取得します。

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

## /simplify

変更されたコードの重複・複雑さ・非効率を検出し、自動改善します。

```bash
/simplify
```

改善観点：
- 重複コードの統合
- 不要な抽象化の除去
- 読みやすさの向上
- パフォーマンス改善

---

## /code-review ultra（マルチエージェントレビュー）

複数のサブエージェントが並列でレビューを行う高精度モードです：

```bash
/code-review ultra

# GitHub PR 番号を指定
/code-review ultra 42
```

通常の `/code-review` より深い分析が行われますが、時間とトークンを消費します。

---

## スキルのカスタマイズ

組み込みスキルは上書きできます。`.claude/skills/code-review/SKILL.md` を作成すると、プロジェクト固有の動作に変更できます：

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

## 次のステップ

→ [2-4: カスタムスキル作成](04-custom-skills.md) に進む
