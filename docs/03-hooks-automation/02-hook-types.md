# 3-2: フックの種類

> **学習時間**: 15分 | **難易度**: ⭐⭐

## PreToolUse

ツールが実行される直前に発火します。ツールへの入力は **stdin から JSON** として渡されます。ブロックするには `permissionDecision: "deny"` を含む JSON を stdout に出力します（exit code 2 でも stderr メッセージと共にブロック可能）。

### 設定例: rm -rf をブロック

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'CMD=$(cat | jq -r .tool_input.command); if echo \"$CMD\" | grep -q \"rm -rf\"; then echo \"{\\\"hookSpecificOutput\\\":{\\\"hookEventName\\\":\\\"PreToolUse\\\",\\\"permissionDecision\\\":\\\"deny\\\",\\\"permissionDecisionReason\\\":\\\"rm -rf は危険なためブロックしました\\\"}}\"; fi'"
          }
        ]
      }
    ]
  }
}
```

スクリプトファイルに切り出すと読みやすくなります（`.claude/hooks/block-rm.sh`）：

```bash
#!/bin/bash
COMMAND=$(cat | jq -r '.tool_input.command // empty')
if echo "$COMMAND" | grep -q 'rm -rf'; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: "rm -rf は危険なためブロックしました"
    }
  }'
fi
```

### 設定例: git push 前に確認

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash(git push*)",
        "hooks": [
          {
            "type": "command",
            "command": "scripts/pre-push-check.sh"
          }
        ]
      }
    ]
  }
}
```

---

## PostToolUse

ツールが実行された直後に発火します。自動フォーマット・テスト実行に使います。

### 設定例: ファイル編集後に Prettier を実行

ツールの入力は stdin から JSON で取得します。Edit/Write ツールのファイルパスは `.tool_input.file_path` に入っています。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); npx prettier --write \"$FILE\" 2>/dev/null || true'"
          }
        ]
      }
    ]
  }
}
```

### 設定例: テストファイル変更後に自動テスト

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); if echo \"$FILE\" | grep -q \"\\.test\\.\"; then npm test -- --run; fi'"
          }
        ]
      }
    ]
  }
}
```

---

## Stop

Claude Code がレスポンスを完了したとき（ターンが終わったとき）に発火します。

### 設定例: 完了したら通知音を鳴らす

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "afplay /System/Library/Sounds/Glass.aiff"
          }
        ]
      }
    ]
  }
}
```

### 設定例: 完了時に Slack 通知

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "curl -X POST $SLACK_WEBHOOK -d '{\"text\": \"Claude Code がタスクを完了しました\"}'"
          }
        ]
      }
    ]
  }
}
```

---

## Notification

Claude Code が通知を発生させたとき（承認待ち・エラー等）に発火します。

### 設定例: 承認待ちを通知

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude Code が入力待ちです\" with title \"Claude Code\"'"
          }
        ]
      }
    ]
  }
}
```

---

## UserPromptSubmit

ユーザーがメッセージを送信した直後に発火します。入力の前処理や検証に使います。

### 設定例: 入力をログに記録

UserPromptSubmit の stdin には `{"prompt": "...", "permission_mode": "..."}` の形式で入力が渡されます。

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'PROMPT=$(cat | jq -r .prompt); echo \"$(date): $PROMPT\" >> ~/.claude/prompt-history.log'"
          }
        ]
      }
    ]
  }
}
```

---

## matcher の書き方

| パターン | マッチするツール |
|---------|--------------|
| `"Edit"` | Edit ツールのみ |
| `"Edit|Write"` | Edit または Write（`\|` のエスケープ不要） |
| `"Bash"` | 全 Bash コマンド |
| `"Bash(git *)"` | git で始まる Bash のみ |
| `"mcp__memory__.*"` | memory サーバーの全ツール（正規表現） |
| `""` または省略 | 全ツール |

英数字・`_`・`|` のみで構成されるパターンはパイプ区切りリストとして扱われます。それ以外の文字が含まれる場合は JavaScript 正規表現として解釈されます。

## ツール入力・出力の取得

フックコマンドはツールの入力・出力を **stdin から JSON** として受け取ります（環境変数ではありません）。`jq` を使って値を抽出します：

```bash
# PreToolUse: ツールへの入力コマンドを取得
COMMAND=$(cat | jq -r '.tool_input.command // empty')

# PostToolUse: 編集されたファイルパスを取得
FILE=$(cat | jq -r '.tool_input.file_path // empty')

# UserPromptSubmit: ユーザーの入力テキストを取得
PROMPT=$(cat | jq -r '.prompt // empty')
```

## 環境変数

フックコマンド内で利用できるシステム環境変数：

| 変数 | 利用可能なイベント | 内容 |
|------|------------------|------|
| `CLAUDE_ENV_FILE` | SessionStart | 環境変数を永続化するためのファイルパス |
| `CLAUDE_EFFORT` | ツール使用イベント | 努力レベル（low / medium / high / xhigh / max） |
| `CLAUDE_CODE_REMOTE` | 全イベント | Web 環境で実行中の場合 `"true"` |

コマンド文字列内で使えるパスプレースホルダー：

| プレースホルダー | 内容 |
|----------------|------|
| `${CLAUDE_PROJECT_DIR}` | プロジェクトルートの絶対パス |
| `${CLAUDE_PLUGIN_ROOT}` | プラグインのインストールディレクトリ |
| `${CLAUDE_PLUGIN_DATA}` | プラグインの永続データディレクトリ |

## 次のステップ

→ [3-3: 自動化パターン](03-automation-patterns.md) に進む
