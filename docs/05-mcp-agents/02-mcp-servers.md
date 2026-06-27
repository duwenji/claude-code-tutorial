# 5-2: MCP サーバー設定

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## 設定方法

MCP サーバーは CLI コマンド（`claude mcp add`）または設定ファイルの直接編集で追加できます。

## `claude mcp add` コマンド（推奨）

```bash
# HTTP サーバー（リモート）を追加
claude mcp add --transport http <名前> <URL>

# stdio サーバー（ローカル）を追加
claude mcp add --transport stdio <名前> -- <コマンド> [引数...]

# 環境変数付きで追加
claude mcp add --env KEY=value --transport stdio <名前> -- <コマンド>

# スコープを指定して追加（local/project/user）
claude mcp add --scope project --transport http <名前> <URL>
```

### サーバーの管理コマンド

```bash
# 設定済みサーバーの一覧表示
claude mcp list

# 特定サーバーの詳細表示
claude mcp get github

# サーバーの削除
claude mcp remove github

# OAuth 認証（HTTP サーバー）
claude mcp login sentry

# （Claude Code セッション内）サーバー状態確認
/mcp
```

## 設定スコープ

MCP サーバーの設定は3つのスコープで管理されます：

| スコープ | 有効範囲 | チームへの共有 | 保存先 |
|---------|---------|-------------|-------|
| **local**（デフォルト） | 現在のプロジェクトのみ | なし（非公開） | `~/.claude.json` |
| **project** | 現在のプロジェクトのみ | あり（バージョン管理） | `.mcp.json` |
| **user** | 全プロジェクト | なし（非公開） | `~/.claude.json` |

## 基本的な設定例

### HTTP サーバー（Streamable HTTP / リモート）

```bash
# コマンドで追加
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 認証ヘッダー付き
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

`.mcp.json` に直接書く場合：

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_GITHUB_PAT"
      }
    }
  }
}
```

### stdio サーバー（ローカルプロセス）

```bash
# コマンドで追加
claude mcp add --transport stdio filesystem -- npx -y @modelcontextprotocol/server-filesystem /Users/me/projects
```

`.mcp.json` に直接書く場合：

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/me/projects"
      ]
    }
  }
}
```

### 環境変数による API キー渡し

```bash
# コマンドで追加（--env で環境変数を渡す）
claude mcp add --env AIRTABLE_API_KEY=YOUR_KEY --transport stdio airtable \
  -- npx -y airtable-mcp-server
```

`.mcp.json` で環境変数を使う場合：

```json
{
  "mcpServers": {
    "slack": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}"
      }
    }
  }
}
```

> `.mcp.json` では `${VAR}` 形式の環境変数展開が使えます。API キーは `.mcp.json` に直書きせず、シェルの環境変数から参照するのが安全です。

## SSE（Server-Sent Events）トランスポートについて

> **注意**: SSE トランスポートは**非推奨**です。新規設定には HTTP トランスポートを使ってください。

後方互換のため既存の SSE サーバーは引き続き動作しますが、新しいリモートサーバーの接続には Streamable HTTP を使用してください：

```bash
# 非推奨（SSE）
claude mcp add --transport sse asana https://mcp.asana.com/sse

# 推奨（HTTP）
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

## 複数サーバーの同時使用（`.mcp.json`）

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/me/projects"]
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": { "Authorization": "Bearer ${GITHUB_PAT}" }
    },
    "sentry": {
      "type": "http",
      "url": "https://mcp.sentry.dev/mcp"
    }
  }
}
```

## OAuth 認証（リモートサーバー）

HTTP サーバーで OAuth 2.0 認証が必要な場合：

```bash
# サーバーを追加
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# OAuth フローを実行（ブラウザが開きます）
claude mcp login sentry

# または Claude Code セッション内で
/mcp
```

## MCP サーバーの動作確認

```bash
> 利用可能な MCP ツールを一覧表示して
```

または Claude Code セッション内で：

```
/mcp
```

GitHub MCP サーバーが正しく設定されていれば：

```
> my-project の未解決 Issue を優先度順に並べて
> Issue #42 に「調査中」のラベルを付けて、担当者を自分に設定して
```

## セキュリティの注意

- API キーは `.mcp.json` に直書きせず、環境変数（`${VAR}`）で参照する
- 個人の API キーは `settings.local.json` や シェルの環境変数で管理し、git 管理外にする
- 必要最小限のスコープ・権限を付与する
- 本番 DB への MCP 接続は慎重に行う
- 各サーバーは信頼できることを確認してから接続する（プロンプトインジェクションリスク）

## 🏋️ 練習問題

1. **【確認】** stdio 型と Streamable HTTP 型の MCP サーバーの違いを説明してください。また、SSE トランスポートの現在の位置づけも答えてください。
2. **【実践】** `@modelcontextprotocol/server-filesystem` を `claude mcp add` コマンドで追加し、`claude mcp list` で確認してみましょう。
3. **【実践】** 設定した MCP サーバーが認識されているか `/mcp` コマンドで確認しましょう。
4. **【応用】** MCP サーバーの API キーを安全に管理するベストプラクティスを説明してください（`.mcp.json` での環境変数展開の活用を含めて）。

## 次のステップ

→ [5-3: エージェント SDK](03-agent-sdk.md) に進む
