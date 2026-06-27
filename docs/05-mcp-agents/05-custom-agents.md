# 5-5: カスタムエージェント実装

> **学習時間**: 30分 | **難易度**: ⭐⭐⭐

## カスタムエージェントとは

特定のタスクに特化したエージェントを定義し、再利用できます。Agent SDK では `agents` オプションで直接定義する方法と、Claude Code のスキル（`.claude/skills/` ディレクトリ）を利用する方法があります。

## カスタムエージェントの設計原則

1. **単一責務**: 1つのエージェントは1つのことをうまくやる
2. **ツール制限**: 必要最小限のツールだけ許可する
3. **明確なインターフェース**: 入力と出力の形式を明確にする
4. **エラーハンドリング**: 失敗ケースを想定した設計

## Agent SDK でのカスタムエージェント定義

### TypeScript

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "code-reviewer エージェントを使ってこのコードをレビューして",
  options: {
    allowedTools: ["Read", "Glob", "Grep", "Agent"],
    agents: {
      "code-reviewer": {
        description: "コード品質とセキュリティのエキスパートレビュアー。コードレビューを依頼されたときに使う。",
        prompt: "コード品質を分析し、バグ・セキュリティ問題・改善点を報告する専門エージェントです。",
        tools: ["Read", "Glob", "Grep"],
      },
      "doc-generator": {
        description: "JSDoc / docstring を自動生成する専門エージェント。",
        prompt: "コードを読み取り、適切なドキュメントコメントを生成します。",
        tools: ["Read"],
      },
    },
  },
})) {
  if ("result" in message) console.log(message.result);
}
```

### Python

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async def main():
    async for message in query(
        prompt="code-reviewer エージェントを使ってこのコードをレビューして",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Glob", "Grep", "Agent"],
            agents={
                "code-reviewer": AgentDefinition(
                    description="コード品質とセキュリティのエキスパートレビュアー。",
                    prompt="コード品質を分析し、バグ・セキュリティ問題・改善点を報告します。",
                    tools=["Read", "Glob", "Grep"],
                ),
                "doc-generator": AgentDefinition(
                    description="docstring を自動生成する専門エージェント。",
                    prompt="コードを読み取り、適切なドキュメントコメントを生成します。",
                    tools=["Read"],
                ),
            },
        ),
    ):
        if hasattr(message, "result"):
            print(message.result)

asyncio.run(main())
```

> サブエージェントを呼び出すには `allowedTools`（Python: `allowed_tools`）に `"Agent"` を含める必要があります。

## 実装例: PR レビューエージェント

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

interface PRReviewResult {
  score: number;
  issues: Array<{
    severity: "critical" | "major" | "minor";
    file: string;
    line: number;
    message: string;
  }>;
  approved: boolean;
  summary: string;
}

async function prReviewAgent(prDiff: string): Promise<PRReviewResult> {
  let resultText = "";

  for await (const message of query({
    prompt: `
以下の PR diff を厳密にレビューして、JSON で結果を返して。

## レビュー観点
1. バグ・ロジックエラー（critical）
2. セキュリティ問題（critical）
3. パフォーマンス問題（major）
4. コード品質（minor）

## 出力形式（必ずこの JSON のみを返すこと）
{
  "score": 0-100,
  "issues": [
    {
      "severity": "critical|major|minor",
      "file": "ファイルパス",
      "line": 行番号,
      "message": "問題の説明"
    }
  ],
  "approved": true/false,
  "summary": "1〜2文のサマリー"
}

## Diff
${prDiff}
    `,
    options: {
      allowedTools: ["Read", "Grep"],
      maxTurns: 5,
    },
  })) {
    if ("result" in message) resultText = message.result;
  }

  const jsonMatch = resultText.match(/\{[\s\S]*\}/);
  if (!jsonMatch) throw new Error("Invalid response format");

  return JSON.parse(jsonMatch[0]) as PRReviewResult;
}
```

## 実装例: ドキュメント生成エージェント

```typescript
async function docGeneratorAgent(filePath: string): Promise<string> {
  let resultText = "";

  for await (const message of query({
    prompt: `
${filePath} を読んで、以下の形式で JSDoc コメントを生成して。
コメントのみを返し、コード自体は変更しないこと。

形式:
/**
 * 関数の説明
 * @param {型} 引数名 - 説明
 * @returns {型} 返り値の説明
 * @example
 * // 使用例
 */
    `,
    options: {
      allowedTools: ["Read"],
      maxTurns: 3,
    },
  })) {
    if ("result" in message) resultText = message.result;
  }

  return resultText;
}
```

## エージェントのパイプライン化

複数のエージェントをパイプラインとして繋げます：

```typescript
async function qualityPipeline(files: string[]) {
  for (const file of files) {
    // Step 1: レビュー
    const review = await prReviewAgent(await getFileDiff(file));

    if (review.issues.some((i) => i.severity === "critical")) {
      // Step 2: 自動修正（critical のみ）
      for await (const msg of query({
        prompt: `${file} の以下の問題を修正して：${JSON.stringify(review.issues.filter((i) => i.severity === "critical"))}`,
        options: { maxTurns: 10 },
      })) {
        // 進捗をログ
      }

      // Step 3: 修正後に再レビュー
      const reReview = await prReviewAgent(await getFileDiff(file));
      console.log(`${file}: ${reReview.approved ? "OK" : "NG"}`);
    }
  }
}
```

## 専門エージェント vs 汎用エージェントの比較

| 観点 | 専門エージェント（複数） | 汎用エージェント（単一） |
|------|----------------------|----------------------|
| 精度 | 高い（タスクに特化） | 中程度 |
| コスト | 高い（複数回の呼び出し） | 低い（1回で完結） |
| 再利用性 | 高い（複数箇所で使い回せる） | 低い |
| 保守性 | 明確な責務分担で管理しやすい | シンプルで管理が楽 |
| 適用場面 | 複雑・大規模なタスク | シンプルな一回限りのタスク |

## GitHub Actions でのカスタムエージェント

```yaml
name: AI Quality Gate
on: [pull_request]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run AI Review Agent
        run: node scripts/pr-review-agent.js
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          PR_DIFF: ${{ github.event.pull_request.diff_url }}
```

## 🏋️ 練習問題

1. **【確認】** Agent SDK でカスタムエージェントを定義する際に必要なフィールド（`description`・`prompt`・`tools`）の役割をそれぞれ説明してください。
2. **【実践】** 利用可能なツールを `Read` と `Grep` のみに限定した「コードリーダー」エージェントを Agent SDK で定義し、`Agent` ツール経由で呼び出すスクリプトを書いてみましょう。
3. **【実践】** 定義したカスタムエージェントを実際に呼び出し、結果を確認してみましょう。
4. **【応用】** 専門エージェントを複数用意するメリットと、単一の汎用エージェントを使うメリットを比較し、どのような場合にどちらを選ぶべきか説明してください。

## 次のステップ

→ [6-1: CI/CD 統合](../06-advanced/01-ci-cd-integration.md) に進む
