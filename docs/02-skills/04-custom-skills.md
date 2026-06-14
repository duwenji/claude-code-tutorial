# 2-4: カスタムスキル作成

> **学習時間**: 25分 | **難易度**: ⭐⭐

## 手書き SKILL.md の基本

`/skill-creator` を使わず、テキストエディタで直接 SKILL.md を作成することもできます。シンプルなスキルや、細かい制御が必要な場合に適しています。

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

### 補助ファイルの活用

複雑なスキルは補助スクリプトを分離できます：

```
.claude/skills/deploy-check/
├── SKILL.md
├── scripts/
│   ├── check-env.sh
│   └── run-security-scan.sh
└── references/
    └── deployment-checklist.md
```

`SKILL.md` 内で参照する：
```markdown
## 環境変数チェック
`scripts/check-env.sh` を実行して環境変数の状態を確認する。
```

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

## 次のステップ

→ [3-1: フックの概要](../03-hooks-automation/01-hooks-overview.md) に進む
