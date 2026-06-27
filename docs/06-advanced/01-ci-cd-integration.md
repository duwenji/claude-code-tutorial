# 6-1: CI/CD 統合

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## 概要

Claude Code を CI/CD パイプラインに組み込むことで、コードレビューや品質チェックを自動化できます。公式の `claude-code-action` を使うと、GitHub PR やイシューへの `@claude` メンションをトリガーにして AI による自動実装・レビューが行えます。

## GitHub Actions での基本的な使い方

### クイックセットアップ（推奨）

Claude Code のターミナルで以下を実行すると、GitHub App のインストールからワークフロー設定まで対話式に完結します：

```
/install-github-app
```

> **注意**: リポジトリの管理者権限が必要です。Amazon Bedrock や Google Vertex AI を使用する場合は後述の手動セットアップが必要です。

### 手動セットアップ

1. **Claude GitHub App をインストール**: [https://github.com/apps/claude](https://github.com/apps/claude)
   - 必要な権限: Contents・Issues・Pull requests（Read & Write）
2. **`ANTHROPIC_API_KEY` をリポジトリシークレットに追加**
3. **ワークフローファイルをコピー**: [examples/claude.yml](https://github.com/anthropics/claude-code-action/blob/main/examples/claude.yml)

### 基本ワークフロー（`@claude` メンション対応）

```yaml
# .github/workflows/claude.yml
name: Claude Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]

jobs:
  claude:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      issues: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # PR/イシューコメントで @claude を呼ぶと自動で応答する
```

コメントで `@claude` をメンションするだけで Claude が動作します：

```
@claude この機能を実装して
@claude このバグを修正して
@claude セキュリティレビューをお願い
```

### PR 自動コードレビュー（スケジュール実行）

PR が開かれたタイミングで自動的にレビューを実行する場合は `prompt` パラメーターを使います：

```yaml
# .github/workflows/ai-review.yml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "この PR をレビューして。バグ・セキュリティ・パフォーマンスの問題を報告して"
          claude_args: "--max-turns 5"
```

### Claude Agent SDK を使ったカスタム統合

より高度なカスタム処理が必要な場合は、`@anthropic-ai/claude-agent-sdk` を使ってスクリプトを書きます：

```javascript
// scripts/review.js
const { query } = require("@anthropic-ai/claude-agent-sdk");
const { Octokit } = require("@octokit/rest");
const fs = require("fs");

async function main() {
  const diff = fs.readFileSync("/tmp/pr.diff", "utf8");

  const result = await query({
    prompt: `
以下の PR diff をレビューして。
重大な問題（バグ・セキュリティ・パフォーマンス）のみを報告して。
JSON 形式で返して。

${diff}
    `,
    options: {
      allowedTools: ["Read", "Grep"],
      maxTurns: 5,
    },
  });

  const issues = JSON.parse(result.result);

  if (issues.critical_count > 0) {
    console.error("Critical issues found");
    process.exit(1);
  }

  console.log("Review passed");
}

main().catch(console.error);
```

## GitLab CI での使用

### GitLab CI の基本概念

GitLab CI は `.gitlab-ci.yml` という1つのファイルでパイプライン全体を定義します。

| 用語 | 意味 | GitHub Actions との対応 |
|------|------|------------------------|
| **Pipeline** | CI 全体の実行単位 | Workflow |
| **Stage** | パイプラインの段階（例: build → test → review） | - |
| **Job** | 各ステージ内の個別タスク | Job |
| **Runner** | ジョブを実行するサーバー | Runner |
| **Merge Request (MR)** | コードレビューの単位 | Pull Request (PR) |

### API キーの設定方法

GitLab プロジェクトの **Settings → CI/CD → Variables** で以下を登録します：

| 変数名 | 値 | オプション |
|--------|----|----------|
| `ANTHROPIC_API_KEY` | `sk-ant-...` | Masked（ログに表示しない） |

### 基本設定

```yaml
# .gitlab-ci.yml
ai-review:
  stage: review
  image: node:20
  script:
    - npm install -g @anthropic-ai/claude-agent-sdk
    - git diff origin/main...HEAD > /tmp/pr.diff
    - node scripts/review.js
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY
  only:
    - merge_requests
```

### 設定の各項目の意味

| キー | 説明 |
|------|------|
| `stage: review` | `stages:` で定義したステージの1つに配置する |
| `image: node:20` | ジョブを実行する Docker イメージ（Node.js 20） |
| `script:` | 実行するシェルコマンドのリスト（上から順に実行） |
| `variables:` | ジョブ内で使う環境変数（`$変数名` で GitLab CI 変数を参照） |
| `only: merge_requests` | マージリクエストが作成・更新されたときだけ実行 |

### 複数ステージを使った実践的な構成

```yaml
# .gitlab-ci.yml（マルチステージ版）
stages:
  - build
  - test
  - review   # ← AI レビューはここ
  - deploy

ai-review:
  stage: review
  image: node:20
  script:
    - npm install -g @anthropic-ai/claude-agent-sdk
    - git diff origin/main...HEAD > /tmp/pr.diff
    - node scripts/review.js
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY
  artifacts:
    paths:
      - review-report.json   # レポートを次のジョブに引き継ぐ
    expire_in: 1 week
  only:
    - merge_requests
```

> **ポイント**: `artifacts` を使うとレビュー結果ファイルを保存・ダウンロードできます。

### GitLab と GitHub Actions の比較

| 項目 | GitLab CI | GitHub Actions |
|------|-----------|----------------|
| 設定ファイル | `.gitlab-ci.yml`（1ファイル） | `.github/workflows/*.yml`（複数可） |
| トリガー | `only: merge_requests` | `on: pull_request:` |
| シークレット | Settings → CI/CD → Variables | Settings → Secrets |
| セルフホスト実行環境 | GitLab Runner | Self-hosted Runner |

## 品質ゲートとしての使用

コードレビューが通らなければマージをブロックするパターン：

```yaml
# GitHub Actions
- name: AI Security Gate
  uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: "このブランチの変更をセキュリティレビューして。HIGH 以上の問題があれば最後に SECURITY_GATE_FAILED と出力して"
    claude_args: "--max-turns 5"
```

## コスト管理

CI/CD で使用する場合はコストに注意：

| 戦略 | 説明 |
|------|------|
| `--max-turns` を制限 | `claude_args: "--max-turns 5"` でターン数を制限 |
| 軽量モデルを使う | `claude_args: "--model claude-haiku-4-5"` を CI/CD に |
| 変更ファイルのみ対象 | diff ファイルのみを読む |
| ワークフロータイムアウト設定 | 暴走ジョブを防ぐために `timeout-minutes` を設定 |
| 並列実行数を制限 | GitHub の concurrency 設定でコスト上限を管理 |

## Amazon Bedrock / Google Vertex AI での利用

企業環境では、`ANTHROPIC_API_KEY` の代わりに自社クラウドを使用できます：

```yaml
# Amazon Bedrock の場合
- uses: anthropics/claude-code-action@v1
  with:
    use_bedrock: "true"
    claude_args: '--model us.anthropic.claude-sonnet-4-6 --max-turns 10'
  # AWS 認証は configure-aws-credentials@v4 アクションで事前設定

# Google Vertex AI の場合
- uses: anthropics/claude-code-action@v1
  with:
    use_vertex: "true"
    claude_args: '--model claude-sonnet-4-5@20250929 --max-turns 10'
  env:
    ANTHROPIC_VERTEX_PROJECT_ID: ${{ steps.auth.outputs.project_id }}
    CLOUD_ML_REGION: us-east5
```

## 🏋️ 練習問題

1. **【確認】** GitHub Actions で Claude Code を使う際、リポジトリシークレットに設定が必要な環境変数名を答えてください。また、`/install-github-app` コマンドで何ができるか説明してください。

2. **【実践】** `claude-code-action@v1` を使った GitHub Actions ワークフロー（PR 作成時に自動でコードレビューを実行）を作成してみましょう。`prompt` パラメーターと `claude_args` の両方を使ってください。

3. **【実践】** `@anthropic-ai/claude-agent-sdk` の `query` 関数を使った CI スクリプトを書いてみましょう。`allowedTools` と `maxTurns` を設定して、diff をレビューするサンプルを実装してください。

4. **【応用】** Claude Code を CI に組み込む際のコスト管理のポイントを2つ挙げてください。`claude_args` でどのように制御できるかも合わせて説明してください。

## 次のステップ

→ [6-2: チームワークフロー](02-team-workflow.md) に進む
