# 5-3: エージェント SDK

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## Claude Agent SDK とは

Claude Agent SDK は、Claude Code のエージェント機能をプログラムから呼び出すための SDK です。カスタムエージェントの構築や、Claude Code の機能を自前のアプリに組み込めます。TypeScript と Python の両方をサポートしています。

## SDK のインストール

```bash
# TypeScript / JavaScript
npm install @anthropic-ai/claude-agent-sdk

# Python（Python 3.10 以上が必要）
pip install claude-agent-sdk
```

> **注意**: パッケージ名が変更されました。旧パッケージ（`@anthropic-ai/claude-code`）ではなく、`@anthropic-ai/claude-agent-sdk` を使用してください。

## 基本的な使い方

### シンプルなエージェント実行

```typescript
// TypeScript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "src/utils/helpers.ts を読んで、改善点を教えて",
  options: {
    maxTurns: 5,
  },
})) {
  if ("result" in message) console.log(message.result);
}
```

```python
# Python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    async for message in query(
        prompt="src/utils/helpers.py を読んで、改善点を教えて",
        options=ClaudeAgentOptions(max_turns=5),
    ):
        if hasattr(message, "result"):
            print(message.result)

asyncio.run(main())
```

### ツールの制限

セキュリティのため、使用するツールを制限できます：

```typescript
// TypeScript
for await (const message of query({
  prompt: "プロジェクトのファイル構造を分析して",
  options: {
    allowedTools: ["Read", "Glob", "Grep"],
    maxTurns: 10,
  },
})) {
  if ("result" in message) console.log(message.result);
}
```

```python
# Python
async for message in query(
    prompt="プロジェクトのファイル構造を分析して",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Glob", "Grep"],
        max_turns=10,
    ),
):
    if hasattr(message, "result"):
        print(message.result)
```

### ストリーミング

結果をリアルタイムでストリーミングできます：

```typescript
// TypeScript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "大きなリファクタリングをして",
  options: { maxTurns: 20 },
})) {
  console.log(message);
}
```

## メッセージタイプ

SDK が返すメッセージの種類：

| タイプ | 内容 |
|-------|------|
| `system` | セッション情報（`subtype: "init"` でセッション ID を取得可能） |
| `assistant` | Claude のレスポンス |
| `user` | ツール実行結果など |
| `result` | 最終結果（成功/失敗）|

## オプション一覧

```typescript
// TypeScript
interface QueryOptions {
  maxTurns?: number;          // 最大ターン数
  allowedTools?: string[];    // 許可するツール
  disallowedTools?: string[]; // 禁止するツール
  systemPrompt?: string;      // システムプロンプトの追加
  cwd?: string;               // 作業ディレクトリ
  model?: string;             // 使用モデル
  permissionMode?: string;    // 権限モード（"acceptEdits" 等）
  mcpServers?: object;        // MCP サーバー設定
  agents?: object;            // カスタムエージェント定義
  resume?: string;            // セッション再開（セッション ID）
}
```

```python
# Python（ClaudeAgentOptions の主なフィールド）
ClaudeAgentOptions(
    max_turns=10,
    allowed_tools=["Read", "Edit"],
    disallowed_tools=[],
    system_prompt="...",
    cwd="/path/to/project",
    model="...",
    permission_mode="acceptEdits",
    mcp_servers={},
    agents={},
    resume="session-id",
)
```

## セッション管理

セッション ID を保持することで、会話の文脈を引き継いで続けられます：

```typescript
// TypeScript
let sessionId: string | undefined;

// 最初のクエリでセッション ID を取得
for await (const message of query({ prompt: "認証モジュールを読んで" })) {
  if (message.type === "system" && message.subtype === "init") {
    sessionId = message.session_id;
  }
}

// セッションを再開して続きを実行
for await (const message of query({
  prompt: "それを呼び出している箇所を全部見つけて",  // 「それ」=認証モジュール
  options: { resume: sessionId },
})) {
  if ("result" in message) console.log(message.result);
}
```

## 実践例: 自動コードレビュースクリプト

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";
import { execSync } from "child_process";

async function reviewPR() {
  const diff = execSync(`git diff main...HEAD`).toString();

  for await (const message of query({
    prompt: `以下の diff をレビューして、問題点を JSON 形式で返して：\n\n${diff}`,
    options: {
      allowedTools: ["Read", "Glob", "Grep"],
      maxTurns: 5,
    },
  })) {
    if ("result" in message) {
      console.log(JSON.parse(message.result));
    }
  }
}
```

## 実践例: CI/CD での使用

```yaml
# .github/workflows/review.yml
- name: AI Code Review
  run: |
    node scripts/ai-review.js
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

```javascript
// scripts/ai-review.js
const { query } = require("@anthropic-ai/claude-agent-sdk");

async function main() {
  for await (const message of query({
    prompt: "このブランチの変更をセキュリティの観点でレビューして",
    options: { maxTurns: 10 },
  })) {
    if ("result" in message && message.result.includes("HIGH_SEVERITY")) {
      process.exit(1);
    }
  }
}

main().catch(console.error);
```

## 🏋️ 練習問題

1. **【確認】** Claude Agent SDK でエージェントを起動する際に最低限必要なパラメータを答えてください。また、TypeScript と Python でパッケージ名がどう違うか確認してください。
2. **【実践】** SDK を使って、指定ファイルの要約を出力する簡単なスクリプトを書いてみましょう（`allowedTools: ["Read"]` に限定して）。
3. **【実践】** `maxTurns` を 3 に設定したエージェントを実行し、ターン数制限がどのように機能するか確認しましょう。
4. **【応用】** SDK でエージェントを使う場合と CLI を直接使う場合の使い分けを考えてみましょう（CI/CD・自動化・対話的作業の観点から）。

## 次のステップ

→ [5-4: マルチエージェント](04-multi-agent.md) に進む
