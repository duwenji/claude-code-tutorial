# 6-1: CI/CD 統合

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## 概要

Claude Code を CI/CD パイプラインに組み込むことで、コードレビューや品質チェックを自動化できます。

## GitHub Actions での基本的な使い方

### PR 自動コードレビュー

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
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run AI Review
        run: |
          git diff origin/main...HEAD > /tmp/pr.diff
          node scripts/review.js
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### レビュースクリプト

```javascript
// scripts/review.js
const { query } = require("@anthropic-ai/claude-code");
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
    - npm install -g @anthropic-ai/claude-code
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
    - npm install -g @anthropic-ai/claude-code
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
  run: |
    node scripts/security-gate.js
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}

# セキュリティ問題があれば exit 1 でジョブを失敗させる
```

```javascript
// scripts/security-gate.js
const { query } = require("@anthropic-ai/claude-code");

async function securityGate() {
  const result = await query({
    prompt: "このブランチの変更をセキュリティレビューして。HIGH 以上の問題があれば FAIL と出力して",
    options: {
      allowedTools: ["Read", "Grep", "Glob"],
      maxTurns: 10,
    },
  });

  if (result.result.includes("FAIL")) {
    console.error("Security gate failed");
    process.exit(1);
  }

  console.log("Security gate passed");
}
```

## コスト管理

CI/CD で使用する場合はコストに注意：

| 戦略 | 説明 |
|------|------|
| maxTurns を制限 | `maxTurns: 3〜5` で制限 |
| 軽量モデルを使う | `claude-haiku-4-5-20251001` を CI/CD に |
| 変更ファイルのみ対象 | diff ファイルのみを読む |
| キャッシュを活用 | 変更がないファイルはスキップ |

## 次のステップ

→ [6-2: チームワークフロー](02-team-workflow.md) に進む
