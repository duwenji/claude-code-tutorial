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
| `PreToolUse` | ツール実行の直前 |
| `PostToolUse` | ツール実行の直後 |
| `Stop` | Claude Code がレスポンスを完了したとき |
| `Notification` | 通知が発生したとき |
| `UserPromptSubmit` | ユーザーがメッセージを送信したとき |

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
| `0` | 成功。処理を続行 |
| `1`（PreToolUse のみ） | ツール実行をブロック |
| その他 | エラーとして記録するが処理は続行 |

## フックの出力

フックのコマンドが標準出力に JSON を出力すると、Claude Code はその内容をコンテキストとして読み込みます：

```bash
#!/bin/bash
# フックスクリプト
echo '{"message": "テスト失敗: UserList.test.tsx"}'
```

Claude Code はこのメッセージを受け取り、次のアクションに反映します。

## 設定ファイルの場所

| ファイル | 適用範囲 |
|---------|---------|
| `~/.claude/settings.json` | 全プロジェクト共通 |
| `.claude/settings.json` | そのプロジェクトのみ |
| `.claude/settings.local.json` | ローカル専用（git 管理外） |

## 次のステップ

→ [3-2: フックの種類](02-hook-types.md) に進む
