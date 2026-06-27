# 5-4: マルチエージェント

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## マルチエージェントとは

複数の Claude エージェントを並列または順次に実行し、複雑なタスクを効率的に処理する手法です。Claude Code はメインエージェント（オーケストレーター）からサブエージェントを起動できます。

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
| **Agent SDK（直接）** | 自分のスクリプト | 必要（TypeScript/Python） | 独自アプリに組み込む場合 |

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
// TypeScript
import { query } from "@anthropic-ai/claude-agent-sdk";

async function parallelReview(files: string[]) {
  // 複数ファイルを並列でレビュー
  const reviews = await Promise.all(
    files.map((file) => {
      const messages: string[] = [];
      return (async () => {
        for await (const message of query({
          prompt: `${file} をセキュリティレビューして JSON で返して`,
          options: {
            allowedTools: ["Read"],
            maxTurns: 3,
          },
        })) {
          if ("result" in message) messages.push(message.result);
        }
        return messages[messages.length - 1];
      })();
    })
  );

  return reviews.map((r) => JSON.parse(r ?? "{}"));
}

// 使用例：3ファイルを並列レビュー
const results = await parallelReview([
  "src/auth/login.ts",
  "src/auth/session.ts",
  "src/api/users.ts",
]);
```

```python
# Python
import asyncio
import json
from claude_agent_sdk import query, ClaudeAgentOptions

async def review_file(file: str) -> str:
    result = ""
    async for message in query(
        prompt=f"{file} をセキュリティレビューして JSON で返して",
        options=ClaudeAgentOptions(allowed_tools=["Read"], max_turns=3),
    ):
        if hasattr(message, "result"):
            result = message.result
    return result

async def parallel_review(files: list[str]):
    results = await asyncio.gather(*[review_file(f) for f in files])
    return [json.loads(r) for r in results]
```

## オーケストレーターとサブエージェントのパターン

```typescript
// TypeScript: オーケストレーター
async function orchestrate() {
  // Step 1: 調査エージェント（並列）
  const collectResults = async (prompt: string) => {
    let result = "";
    for await (const msg of query({ prompt, options: { maxTurns: 5 } })) {
      if ("result" in msg) result = msg.result;
    }
    return result;
  };

  const [authIssues, apiIssues, dbIssues] = await Promise.all([
    collectResults("認証コードの問題を調査して"),
    collectResults("API エンドポイントの問題を調査して"),
    collectResults("DB クエリの問題を調査して"),
  ]);

  // Step 2: 修正エージェント（調査結果を基に）
  const fixPrompt = `
    以下の問題を修正して：
    - 認証: ${authIssues}
    - API: ${apiIssues}
    - DB: ${dbIssues}
  `;

  for await (const msg of query({ prompt: fixPrompt, options: { maxTurns: 20 } })) {
    if ("result" in msg) console.log(msg.result);
  }
}
```

## カスタムサブエージェントの定義

Agent SDK では、`agents` オプションで専門エージェントを定義して呼び出せます：

```typescript
// TypeScript
for await (const message of query({
  prompt: "code-reviewer エージェントを使ってコードをレビューして",
  options: {
    allowedTools: ["Read", "Glob", "Grep", "Agent"],
    agents: {
      "code-reviewer": {
        description: "コード品質とセキュリティのエキスパートレビュアー",
        prompt: "コード品質を分析し、改善点を提案する専門エージェントです。",
        tools: ["Read", "Glob", "Grep"],
      },
    },
  },
})) {
  if ("result" in message) console.log(message.result);
}
```

> サブエージェントを呼び出すには `allowedTools` に `"Agent"` を含める必要があります。

## ワークツリーによる並列開発

Git ワークツリーを使うと、同じリポジトリの別のブランチで複数のエージェントが並列作業できます：

```
> 2つのエージェントを使って、
> エージェント1: パフォーマンス改善
> エージェント2: セキュリティ修正
> を並列で行って、最後にマージして
```

## 並列実行 vs 直列実行の使い分け

| 実行方法 | 適切な場面 |
|---------|----------|
| **並列実行** | 独立したタスク（互いの結果に依存しない）、高速化が必要な場合 |
| **直列実行** | 前の結果を次のステップで使う場合、順序が重要な場合 |

## 注意事項

- サブエージェントはそれぞれコストが発生する
- 並列実行はコンテキストを共有しない（各エージェントは独立した会話）
- 結果の統合はオーケストレーターが責任を持つ
- エラー伝播に注意する（1つの失敗が全体に影響しないよう設計する）
- コスト超過に注意し、`maxTurns` で上限を設定する

## 🏋️ 練習問題

1. **【確認】** オーケストレーターとサブエージェントの役割の違いを説明してください。
2. **【確認】** サブエージェントを並列実行する場合と直列実行する場合の使い分けを答えてください。
3. **【実践】** 2つの独立したタスク（例: 「src/auth/ のセキュリティ問題を調査」と「src/api/ のパフォーマンス問題を調査」）を並列サブエージェントで実行するスクリプトを作成してみましょう。
4. **【応用】** マルチエージェントシステムを設計する際のリスク（コスト・エラー伝播・コンテキスト非共有等）を2つ挙げ、それぞれの対策を説明してください。

## 次のステップ

→ [5-5: カスタムエージェント実装](05-custom-agents.md) に進む
