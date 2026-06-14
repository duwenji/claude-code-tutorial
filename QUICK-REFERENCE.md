# Quick Reference — Claude Code チートシート

## スラッシュコマンド

| コマンド | 説明 |
|---------|------|
| `/help` | ヘルプを表示 |
| `/clear` | 会話履歴をクリア |
| `/config` | 設定を変更 |
| `/status` | 現在のステータスを表示 |
| `/code-review` | コードレビュースキルを実行 |
| `/debug` | デバッグスキルを実行 |
| `/run` | アプリを起動して動作確認 |
| `/security-review` | セキュリティレビュー |
| `/skill-creator` | 新しいスキルを対話生成 |
| `/fast` | Fast モード（Opus）切り替え |
| `/init` | CLAUDE.md を生成 |

## スキル配置場所

| 種類 | パス |
|------|------|
| 個人用スキル | `~/.claude/skills/<name>/SKILL.md` |
| プロジェクト用スキル | `.claude/skills/<name>/SKILL.md` |

## SKILL.md 基本テンプレート

```markdown
---
name: my-skill
description: このスキルが何をするかの説明
---

# スキル名

## 概要
スキルの概要。

## 手順
1. ステップ1
2. ステップ2

## 出力形式
期待する出力形式。
```

## Hooks イベント一覧

| イベント | タイミング |
|---------|----------|
| `PreToolUse` | ツール実行前 |
| `PostToolUse` | ツール実行後 |
| `Stop` | Claude が停止したとき |
| `Notification` | 通知発生時 |
| `UserPromptSubmit` | ユーザー入力送信時 |

## settings.json 構造

```json
{
  "model": "claude-opus-4-8",
  "permissions": {
    "allow": ["Bash(git *)", "Read"],
    "deny": ["Bash(rm -rf *)"]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "npm test" }]
      }
    ]
  },
  "env": {
    "MY_VAR": "value"
  }
}
```

## MCP サーバー設定（settings.json）

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "ghp_..." }
    }
  }
}
```

## CLAUDE.md 推奨構造

```markdown
# プロジェクト名

## 概要
プロジェクトの概要。

## 技術スタック
- 言語: TypeScript
- フレームワーク: React

## よく使うコマンド
- `npm run dev` — 開発サーバー起動
- `npm test` — テスト実行

## コーディング規約
- ...

## 注意事項
- ...
```

## キーボードショートカット（VS Code 拡張）

| ショートカット | 動作 |
|-------------|------|
| `Ctrl+Shift+C` | Claude Code パネルを開く |
| `Ctrl+Enter` | メッセージ送信 |
| `Escape` | キャンセル |
