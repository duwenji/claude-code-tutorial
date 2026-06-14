# 5-4: マルチエージェント

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## マルチエージェントとは

複数の Claude エージェントを並列または順次に実行し、複雑なタスクを効率的に処理する手法です。Claude Code はメインエージェントからサブエージェントを起動できます。

## なぜマルチエージェントが必要か

| 問題 | マルチエージェントによる解決 |
|------|--------------------------|
| コンテキストウィンドウの上限 | タスクを分割して各エージェントに割り当て |
| 複数の独立した調査 | 並列実行で高速化 |
| 専門的なレビュー | 役割特化したエージェントを使い分け |
| 大規模コードベース | 並列に異なる部分を分析 |

## Claude Code での使い方

メインの会話から Agent ツールでサブエージェントを起動します：

```
> src/components/ 以下の全コンポーネントをセキュリティレビューして。
> 並列で複数のエージェントを使って高速化して
```

Claude Code は自動的にサブエージェントを起動します。

## Agent SDK でのマルチエージェント

```typescript
import { query } from "@anthropic-ai/claude-code";

async function parallelReview(files: string[]) {
  // 複数ファイルを並列でレビュー
  const reviews = await Promise.all(
    files.map((file) =>
      query({
        prompt: `${file} をセキュリティレビューして JSON で返して`,
        options: {
          allowedTools: ["Read"],
          maxTurns: 3,
        },
      })
    )
  );

  return reviews.map((r) => JSON.parse(r.result));
}

// 使用例
const results = await parallelReview([
  "src/auth/login.ts",
  "src/auth/session.ts",
  "src/api/users.ts",
]);
```

## オーケストレーターとサブエージェントのパターン

```typescript
// オーケストレーター
async function orchestrate() {
  // Step 1: 調査エージェント（並列）
  const [authIssues, apiIssues, dbIssues] = await Promise.all([
    query({ prompt: "認証コードの問題を調査して", ... }),
    query({ prompt: "API エンドポイントの問題を調査して", ... }),
    query({ prompt: "DB クエリの問題を調査して", ... }),
  ]);

  // Step 2: 修正エージェント（調査結果を基に）
  const fixPrompt = `
    以下の問題を修正して：
    - 認証: ${authIssues.result}
    - API: ${apiIssues.result}
    - DB: ${dbIssues.result}
  `;

  const fix = await query({
    prompt: fixPrompt,
    options: { maxTurns: 20 },
  });

  return fix;
}
```

## 特化型エージェントパターン

Claude Code では `subagent_type` で専門エージェントを選べます：

| エージェント | 用途 |
|------------|------|
| `Explore` | コードベースの調査・検索 |
| `Plan` | 実装計画の立案 |
| `claude` | 汎用エージェント |

```
> Explore エージェントを使って、src/ 全体の認証関連コードを調査して
```

## ワークツリーによる並列開発

Git ワークツリーを使うと、同じリポジトリの別のブランチで複数のエージェントが並列作業できます：

```
> 2つのエージェントを使って、
> エージェント1: パフォーマンス改善
> エージェント2: セキュリティ修正
> を並列で行って、最後にマージして
```

## 注意事項

- サブエージェントはそれぞれコストが発生する
- 並列実行はコンテキストを共有しない
- 結果の統合はオーケストレーターが責任を持つ
- デッドロック（相互待ち）に注意する

## 次のステップ

→ [5-5: カスタムエージェント実装](05-custom-agents.md) に進む
