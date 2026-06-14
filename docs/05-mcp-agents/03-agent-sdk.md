# 5-3: エージェント SDK

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## Claude Agent SDK とは

Claude Agent SDK は、Claude Code のエージェント機能をプログラムから呼び出すための SDK です。カスタムエージェントの構築や、Claude Code の機能を自前のアプリに組み込めます。

## SDK のインストール

```bash
npm install @anthropic-ai/claude-code
```

## 基本的な使い方

### シンプルなエージェント実行

```typescript
import { query } from "@anthropic-ai/claude-code";

const result = await query({
  prompt: "src/utils/helpers.ts を読んで、改善点を教えて",
  options: {
    maxTurns: 5,
  },
});

console.log(result.result);
```

### ツールの制限

セキュリティのため、使用するツールを制限できます：

```typescript
const result = await query({
  prompt: "プロジェクトのファイル構造を分析して",
  options: {
    allowedTools: ["Read", "Glob", "Grep"],
    maxTurns: 10,
  },
});
```

### ストリーミング

結果をリアルタイムでストリーミングできます：

```typescript
import { query, type SDKMessage } from "@anthropic-ai/claude-code";

for await (const message of query({
  prompt: "大きなリファクタリングをして",
  options: { maxTurns: 20 },
})) {
  if (message.type === "assistant") {
    process.stdout.write(message.message.content);
  }
}
```

## メッセージタイプ

SDK が返すメッセージの種類：

| タイプ | 内容 |
|-------|------|
| `assistant` | Claude のレスポンス |
| `tool_use` | ツール呼び出し |
| `tool_result` | ツールの実行結果 |
| `result` | 最終結果（成功/失敗） |
| `error` | エラー情報 |

## オプション一覧

```typescript
interface QueryOptions {
  maxTurns?: number;          // 最大ターン数（デフォルト: 10）
  allowedTools?: string[];    // 許可するツール
  disallowedTools?: string[]; // 禁止するツール
  systemPrompt?: string;      // システムプロンプトの追加
  cwd?: string;               // 作業ディレクトリ
  model?: string;             // 使用モデル
}
```

## 実践例: 自動コードレビュースクリプト

```typescript
import { query } from "@anthropic-ai/claude-code";
import { execSync } from "child_process";

async function reviewPR(prNumber: number) {
  const diff = execSync(`git diff main...HEAD`).toString();

  const result = await query({
    prompt: `以下の diff をレビューして、問題点を JSON 形式で返して：\n\n${diff}`,
    options: {
      allowedTools: ["Read", "Glob", "Grep"],
      maxTurns: 5,
    },
  });

  return JSON.parse(result.result);
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
const { query } = require("@anthropic-ai/claude-code");

async function main() {
  const result = await query({
    prompt: "このブランチの変更をセキュリティの観点でレビューして",
    options: { maxTurns: 10 },
  });

  if (result.result.includes("HIGH_SEVERITY")) {
    process.exit(1);
  }
}

main().catch(console.error);
```

## 次のステップ

→ [5-4: マルチエージェント](04-multi-agent.md) に進む
