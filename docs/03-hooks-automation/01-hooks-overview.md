# 3-1: フックの概要

> **学習時間**: 15分 | **難易度**: ⭐⭐

## フック（Hooks）とは

フックは、Claude Code の特定のイベントに対して自動的にシェルコマンドを実行する仕組みです。Claude Code が行動するたびに、決められた処理を自動で挟み込めます。

## フックで実現できること

| 用途 | 例 |
|------|-----|
| 自動フォーマット | ファイル編集後に Prettier を実行 |
| 自動テスト | コード変更後に npm test を実行 |
| 通知 | Claude が完了したら Slack に通知 |
| ログ記録 | 全ツール呼び出しをファイルに記録 |
| セキュリティ | 危険なコマンドをブロック |
| 品質ゲート | lint が通らなければ編集を拒否 |

## フックのイベント種類

| イベント | 発火タイミング |
|---------|-------------|
| `PreToolUse` | ツール実行の直前（ブロック可能） |
| `PostToolUse` | ツール実行の直後 |
| `Stop` | Claude Code がレスポンスを完了したとき |
| `SubagentStop` | サブエージェントが完了したとき |
| `Notification` | 通知が発生したとき |
| `UserPromptSubmit` | ユーザーがメッセージを送信したとき（ブロック可能） |
| `PreCompact` | コンテキスト圧縮の直前（ブロック可能） |

## 基本的な設定方法

フックは `settings.json` に記述します：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npm run format"
          }
        ]
      }
    ]
  }
}
```

この例では、`Edit` ツール（ファイル編集）の直後に `npm run format` が自動実行されます。

## フックの実行結果

フックのコマンドは、exit code で成功/失敗を伝えます：

| exit code | 意味 |
|-----------|------|
| `0` | 成功。stdout の JSON を解析して処理続行 |
| `2` | ブロックエラー。イベントに応じてアクションをブロック（PreToolUse ではツール実行をブロック、UserPromptSubmit ではプロンプトをブロック等） |
| その他 | 非ブロックエラー。stderr の先頭行をトランスクリプトに表示するが処理は続行 |

## フックの出力

フックのコマンドが標準出力に JSON を出力すると、Claude Code はその内容を解析して動作を制御できます。主な出力フィールド：

```json
{
  "continue": true,
  "stopReason": "終了理由（continue: false の場合）",
  "suppressOutput": false,
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow|deny|ask|defer",
    "permissionDecisionReason": "理由の説明",
    "additionalContext": "Claude に渡す追加コンテキスト"
  }
}
```

たとえば PreToolUse でツールをブロックするには：

```bash
#!/bin/bash
jq -n '{
  hookSpecificOutput: {
    hookEventName: "PreToolUse",
    permissionDecision: "deny",
    permissionDecisionReason: "このコマンドは禁止されています"
  }
}'
```

Claude Code はこの JSON を受け取り、次のアクションに反映します。

## 設定ファイルの場所

| ファイル | 適用範囲 |
|---------|---------|
| `~/.claude/settings.json` | 全プロジェクト共通 |
| `.claude/settings.json` | そのプロジェクトのみ |
| `.claude/settings.local.json` | ローカル専用（git 管理外） |

## 🏋️ 練習問題

1. **【確認】** フックが使える7つのイベントを全て答えてください。
2. **【確認】** PreToolUse フックで exit code 2 を返すと何が起きますか？
3. **【実践】** `~/.claude/settings.json` を開き、現在のフック設定を確認しましょう（なければ空の `hooks: {}` を確認）。
4. **【応用】** ファイル編集後に自動でフォーマットを実行するフックを設定するには、どのイベントとどのマッチャーを使いますか？

## 次のステップ

→ [3-2: フックの種類](02-hook-types.md) に進む
