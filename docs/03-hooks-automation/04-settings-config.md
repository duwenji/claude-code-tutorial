# 3-4: settings.json 設定

> **学習時間**: 15分 | **難易度**: ⭐⭐

## settings.json の場所

| ファイル | 適用範囲 | 用途 |
|---------|---------|------|
| `~/.claude/settings.json` | グローバル | 全プロジェクト共通 |
| `.claude/settings.json` | プロジェクト | チームで共有する設定 |
| `.claude/settings.local.json` | プロジェクトローカル | 個人設定（git 管理外） |

## 完全な設定例

```json
{
  "model": "claude-sonnet-4-6",
  "theme": "dark",
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm run *)",
      "Bash(npm test)",
      "Bash(npx prettier *)",
      "Bash(npx eslint *)",
      "Read(**)",
      "Edit(**)",
      "Write(**)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(DROP TABLE *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$CLAUDE_TOOL_OUTPUT_PATH\" 2>/dev/null || true"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Task completed'"
          }
        ]
      }
    ]
  },
  "env": {
    "NODE_ENV": "development",
    "LOG_LEVEL": "debug"
  },
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/me/projects"]
    }
  }
}
```

## 各設定項目の説明

### model

使用するモデルを指定します：

```json
{
  "model": "claude-opus-4-8"
}
```

| モデル | 特徴 |
|-------|------|
| `claude-opus-4-8` | 最高性能 |
| `claude-sonnet-4-6` | バランス型（デフォルト） |
| `claude-haiku-4-5-20251001` | 高速・低コスト |

### permissions

ツールの自動承認・拒否を設定します：

```json
{
  "permissions": {
    "allow": ["Bash(git *)", "Read(**)"],
    "deny": ["Bash(rm -rf *)"]
  }
}
```

**パターン書式:**

| パターン例 | 意味 |
|-----------|------|
| `"Bash(git *)"` | `git` で始まる全 Bash コマンド |
| `"Bash(npm run build)"` | 特定のコマンドのみ |
| `"Read(**)"` | 全ファイルの読み取り |
| `"Edit(src/**)"` | src/ 以下のファイル編集のみ |

### env

セッション中に使用する環境変数を設定します：

```json
{
  "env": {
    "DATABASE_URL": "postgresql://localhost/mydb",
    "API_BASE_URL": "http://localhost:3001"
  }
}
```

### theme

UI テーマを設定します：

```json
{
  "theme": "dark"
}
```

選択肢: `"dark"` / `"light"` / `"auto"`

## プロジェクト用 settings.json の例

チームで共有する最小設定：

```json
{
  "permissions": {
    "allow": [
      "Bash(npm *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "npm run format 2>/dev/null || true" }
        ]
      }
    ]
  }
}
```

## settings.local.json の活用

個人の API キーやパスなど、チームと共有しない設定は `settings.local.json` に書きます：

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-...",
    "PERSONAL_ACCESS_TOKEN": "ghp_..."
  },
  "hooks": {
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "afplay /System/Library/Sounds/Glass.aiff" }
        ]
      }
    ]
  }
}
```

## /config コマンドで設定する

`settings.json` を手書きせず、対話的に設定することもできます：

```
/config
```

## 次のステップ

→ [4-1: メモリの種類](../04-memory-system/01-memory-types.md) に進む
