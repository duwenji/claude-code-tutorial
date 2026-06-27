# 2-4: カスタムスキル作成

> **学習時間**: 25分 | **難易度**: ⭐⭐

## 手書き SKILL.md の基本

`/skill-creator` プラグインを使わず、テキストエディタで直接 SKILL.md を作成することもできます。シンプルなスキルや、細かい制御が必要な場合に適しています。

## SKILL.md テンプレート

````markdown
---
name: deploy-check
description: デプロイ前に環境変数・ビルド・テストをチェックします。deploy, production, リリースという言葉で起動します。
tags:
  - deploy
  - ci
  - quality
---

# デプロイ前チェックスキル

## 概要
本番デプロイ前に必要なチェックを自動実行します。

## チェック項目
1. 必須環境変数の確認
2. ビルドの成功確認
3. テストの全パス確認
4. セキュリティスキャン

## 手順

### Step 1: 環境変数チェック
以下の環境変数が設定されているか確認する：
- `DATABASE_URL`
- `API_KEY`
- `NODE_ENV=production`

### Step 2: ビルド実行
```bash
npm run build
```
エラーがあれば報告し、修正を提案する。

### Step 3: テスト実行
```bash
npm test
```
失敗したテストがあれば一覧を表示する。

### Step 4: 結果サマリー
以下の形式で結果を出力する：

| チェック項目 | 結果 | 詳細 |
|------------|------|------|
| 環境変数 | ✅/❌ | ... |
| ビルド | ✅/❌ | ... |
| テスト | ✅/❌ | ... |

## 出力形式
Markdown の表形式でチェック結果を表示する。全項目✅の場合のみ「デプロイ可」と表示する。
````

## 配置とテスト

```bash
mkdir -p .claude/skills/deploy-check/
# SKILL.md を上記の内容で作成

# テスト実行
claude
/deploy-check
```

## フロントマターの全フィールド

フロントマターの全フィールドはオプションです。よく使うフィールドは以下の通りです：

| フィールド | 説明 |
|-----------|------|
| `name` | 一覧表示名（コマンド名はディレクトリ名から決まる） |
| `description` | 自動起動の判断に使う説明（**推奨**） |
| `disable-model-invocation` | `true` でユーザーのみ起動可に |
| `allowed-tools` | 承認なしで使えるツール |
| `context` | `fork` でサブエージェント実行 |
| `argument-hint` | 補完時のヒント（例：`[filename]`） |
| `paths` | スキルが有効なファイルパターン（glob） |

## ベストプラクティス

### description を充実させる

自動トリガーの精度は `description` フィールドに直結します：

```yaml
# 悪い例
description: デプロイチェックをします

# 良い例
description: >
  本番デプロイ前の事前チェックを実行します。
  環境変数・ビルド・テスト・セキュリティを確認します。
  "deploy", "デプロイ", "production", "リリース", "本番" という
  言葉が出たときに自動的に提案します。
```

`description` と `when_to_use` フィールドの合計は 1,536 文字で切り捨てられます。重要なキーワードを先頭に置いてください。

### 手動起動専用にする

デプロイ・コミットなど副作用のある操作は自動起動させないようにします：

```yaml
---
name: deploy
description: アプリを本番環境にデプロイします
disable-model-invocation: true
---
```

### 引数を受け取る

```yaml
---
name: fix-issue
description: GitHub Issue を修正します
disable-model-invocation: true
---

GitHub Issue $ARGUMENTS をコーディング規約に従って修正する。

1. Issue の内容を読む
2. 要件を把握する
3. 修正を実装する
4. テストを書く
5. コミットを作成する
```

`/fix-issue 123` と入力すると `$ARGUMENTS` が `123` に置換されます。

### 明確な出力形式を定義する

曖昧な指示より、具体的な出力形式を定義するほうが一貫した結果が得られます：

````markdown
## 出力形式

必ず以下の JSON 形式で出力すること：
```json
{
  "status": "ok" | "warning" | "error",
  "checks": [
    {
      "name": "チェック名",
      "result": "pass" | "fail" | "warn",
      "message": "詳細メッセージ"
    }
  ],
  "summary": "全体のサマリー"
}
```
````

### 失敗ケースを明示する

```markdown
## エラーハンドリング

- ファイルが見つからない場合: エラーを報告してスキップ
- コマンドが失敗した場合: エラーログ全文を表示
- タイムアウト（30秒以上）の場合: 中断してユーザーに通知
```

### SKILL.md を簡潔に保つ

`SKILL.md` は 500 行以内を目安にします。詳細なリファレンスは別ファイルに分離して参照します：

```
.claude/skills/deploy-check/
├── SKILL.md
├── references/
│   └── deployment-checklist.md
└── scripts/
    ├── check-env.sh
    └── run-security-scan.sh
```

`SKILL.md` 内で参照する：
```markdown
## 環境変数チェック
詳細は [references/deployment-checklist.md](references/deployment-checklist.md) を参照。
`scripts/check-env.sh` を実行して環境変数の状態を確認する。
```

### 動的コンテキスト注入

`` !`コマンド` `` 構文でシェルコマンドを実行し、出力を SKILL.md に埋め込めます：

```yaml
---
name: summarize-changes
description: 未コミットの変更を要約します
---

## 現在の変更
!`git diff HEAD`

## 指示
上記の差分を2〜3点で要約し、リスク（エラー処理漏れ・ハードコード値等）を列挙する。
```

## スキルのテスト方法

1. **直接呼び出し**: `/deploy-check` で手動テスト
2. **自動起動テスト**: スキルの `description` に合う言葉で会話し、自動起動するか確認
3. **フレッシュセッションで確認**: 新しいセッションでテストして作成時のコンテキストの影響を排除
4. **skill-creator プラグイン**: 定量的な評価が必要な場合はプラグインを使う（[2-2](02-skill-creator.md) 参照）

## スキルの共有

### チームで共有する方法

1. `.claude/skills/` をリポジトリに含める（`.gitignore` に追加しない）
2. `README.md` にスキルの説明を記載する

### 個人スキルとして保存する

全プロジェクトで使いたいスキルは個人スキルとして保存：

```bash
mkdir -p ~/.claude/skills/my-workflow/
# SKILL.md を作成
```

## 🏋️ 練習問題

1. **【確認】** SKILL.md の `description` フィールドが重要な理由を説明してください。
2. **【実践】** 自分のよく使う作業（例：PR 説明文の生成）をカスタムスキルとして作成してみましょう。
3. **【実践】** 作成したスキルを `.claude/skills/` に配置し、スラッシュコマンドで呼び出しましょう。
4. **【応用】** スキルの `description` にトリガーキーワードを含めることで、どのようなメリットがありますか？

## 次のステップ

→ [3-1: フックの概要](../03-hooks-automation/01-hooks-overview.md) に進む
