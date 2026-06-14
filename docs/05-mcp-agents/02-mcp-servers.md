# 5-2: MCP サーバー設定

> **学習時間**: 20分 | **難易度**: ⭐⭐⭐

## 設定方法

MCP サーバーは `settings.json` の `mcpServers` セクションに記述します。

## 基本的な設定例

### ファイルシステムサーバー

```json
{
  "mcpServers": {
    "filesystem": {
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

### GitHub サーバー

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxxxxxxxxx"
      }
    }
  }
}
```

### SQLite サーバー

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sqlite",
        "/path/to/database.db"
      ]
    }
  }
}
```

## 複数サーバーの同時使用

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/me/projects"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..." }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": { "SLACK_BOT_TOKEN": "xoxb-..." }
    }
  }
}
```

## 設定項目の説明

| フィールド | 説明 |
|-----------|------|
| `command` | MCP サーバーを起動するコマンド |
| `args` | コマンドの引数（配列） |
| `env` | 環境変数（API キー等） |
| `transport` | 通信方式（`stdio`（デフォルト）/ `sse`） |

## SSE（Server-Sent Events）トランスポート

HTTP ベースで動作するリモート MCP サーバーに接続する場合：

```json
{
  "mcpServers": {
    "remote-server": {
      "transport": "sse",
      "url": "https://my-mcp-server.example.com/sse",
      "headers": {
        "Authorization": "Bearer my-token"
      }
    }
  }
}
```

## MCP サーバーの動作確認

```bash
claude
> 利用可能な MCP ツールを一覧表示して
```

または：

```
> GitHub の自分のリポジトリを一覧表示して
```

GitHub MCP サーバーが正しく設定されていれば、リポジトリ一覧が返ってきます。

## 実践例: GitHub と連携する

```
> my-project の未解決 Issue を優先度順に並べて、
> 今週取り組むべきものをピックアップして
```

```
> Issue #42 に「調査中」のラベルを付けて、担当者を自分に設定して
```

## セキュリティの注意

- API キーは `settings.local.json` に書く（git 管理外）
- 必要最小限の権限（スコープ）を付与する
- 本番 DB への MCP 接続は慎重に

## 次のステップ

→ [5-3: エージェント SDK](03-agent-sdk.md) に進む
