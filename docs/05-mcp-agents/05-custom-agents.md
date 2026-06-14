# 5-5: カスタムエージェント実装

> **学習時間**: 30分 | **難易度**: ⭐⭐⭐

## カスタムエージェントとは

特定のタスクに特化したエージェントを定義し、Claude Code のエコシステムに追加できます。Claude Code 自体が持つエージェントタイプの仕組みを利用します。

## カスタムエージェントの設計原則

1. **単一責務**: 1つのエージェントは1つのことをうまくやる
2. **ツール制限**: 必要最小限のツールだけ許可する
3. **明確なインターフェース**: 入力と出力の形式を明確にする
4. **エラーハンドリング**: 失敗ケースを想定した設計

## 実装例: PR レビューエージェント

```typescript
import { query } from "@anthropic-ai/claude-code";

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
  const result = await query({
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
      model: "claude-opus-4-8",
    },
  });

  const jsonMatch = result.result.match(/\{[\s\S]*\}/);
  if (!jsonMatch) throw new Error("Invalid response format");

  return JSON.parse(jsonMatch[0]) as PRReviewResult;
}
```

## 実装例: ドキュメント生成エージェント

```typescript
async function docGeneratorAgent(filePath: string): Promise<string> {
  const result = await query({
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
  });

  return result.result;
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
      await query({
        prompt: `${file} の以下の問題を修正して：${JSON.stringify(review.issues.filter((i) => i.severity === "critical"))}`,
        options: { maxTurns: 10 },
      });

      // Step 3: 修正後に再レビュー
      const reReview = await prReviewAgent(await getFileDiff(file));
      console.log(`${file}: ${reReview.approved ? "✅" : "❌"}`);
    }
  }
}
```

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

## 次のステップ

→ [6-1: CI/CD 統合](../06-advanced/01-ci-cd-integration.md) に進む
