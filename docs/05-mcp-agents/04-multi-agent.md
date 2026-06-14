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

## 2つの利用方法

マルチエージェントには **用途が異なる2つの呼び出し方**があります：

| 方法 | 誰が呼び出すか | コードは必要か | 用途 |
|------|--------------|-------------|------|
| **チャット（自動）** | Claude が自動判断 | 不要 | Claude Code を使う日常作業 |
| **Agent SDK（直接）** | 自分のスクリプト | 必要（TypeScript/JS） | 独自アプリに組み込む場合 |

## Claude Code での使い方（チャット・コード不要）

チャットで指示するだけで、Claude が自動的にサブエージェントを起動します。ユーザー側でコードを書く必要はありません。

```
> src/components/ 以下の全コンポーネントをセキュリティレビューして。
> 並列で複数のエージェントを使って高速化して
```

Claude Code は指示を解釈し、内部で Agent ツールを呼び出して並列実行します。

## Agent SDK でのマルチエージェント（独自アプリに組み込む場合）

> **前提**: 独自のスクリプトやアプリから Claude Code を制御したい場合に使います。
> 日常的なチャット作業には不要です。

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

  // query() は { result: string, ... } を返す。result がエージェントの最終出力テキスト
  return reviews.map((r) => JSON.parse(r.result));
}

// 使用例：3ファイルを並列レビューし、JSON 配列として結果を受け取る
const results = await parallelReview([
  "src/auth/login.ts",
  "src/auth/session.ts",
  "src/api/users.ts",
]);
// results[0] → login.ts のレビュー結果オブジェクト
// results[1] → session.ts のレビュー結果オブジェクト
// results[2] → users.ts のレビュー結果オブジェクト
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
