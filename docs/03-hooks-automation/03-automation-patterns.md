# 3-3: 自動化パターン

> **学習時間**: 15分 | **難易度**: ⭐⭐

## パターン1: コード品質の自動維持

ファイルを編集するたびに lint と format を自動実行するパターンです。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'npx eslint --fix \"$CLAUDE_TOOL_OUTPUT_PATH\" 2>/dev/null; npx prettier --write \"$CLAUDE_TOOL_OUTPUT_PATH\" 2>/dev/null; true'"
          }
        ]
      }
    ]
  }
}
```

効果: Claude Code がファイルを編集するたびに自動でコードが整形されます。

---

## パターン2: テスト自動実行

コードを変更するたびに関連テストを実行するパターンです。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "scripts/run-related-tests.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# scripts/run-related-tests.sh
CHANGED_FILE="$CLAUDE_TOOL_OUTPUT_PATH"
TEST_FILE="${CHANGED_FILE/src\//src\/}".test.ts

if [ -f "$TEST_FILE" ]; then
  npx vitest run "$TEST_FILE" 2>&1
  exit $?
fi
```

---

## パターン3: Git 操作のガード

force push や main への直接 push をブロックするパターンです。

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "scripts/git-guard.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# scripts/git-guard.sh
INPUT="$CLAUDE_TOOL_INPUT"

# force push をブロック
if echo "$INPUT" | grep -q "push.*--force\|push.*-f"; then
  echo '{"error": "force push は禁止されています。レビューを経てください。"}'
  exit 1
fi

# main への直接 push をブロック
if echo "$INPUT" | grep -qE "push.*origin main|push.*origin master"; then
  echo '{"error": "main への直接 push は禁止されています。PR を作成してください。"}'
  exit 1
fi
```

---

## パターン4: 完了通知

長時間のタスクが完了したときに通知するパターンです。

### macOS の場合

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"タスクが完了しました\" with title \"Claude Code\" sound name \"Glass\"'"
          }
        ]
      }
    ]
  }
}
```

### Windows の場合

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -command \"[System.Console]::Beep(1000, 300)\""
          }
        ]
      }
    ]
  }
}
```

---

## パターン5: 作業ログの記録

すべての Bash コマンドと結果をログに記録するパターンです。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'echo \"[$(date +%Y-%m-%d\\ %H:%M:%S)] CMD: $CLAUDE_TOOL_INPUT\" >> ~/.claude/activity.log'"
          }
        ]
      }
    ]
  }
}
```

---

## パターン6: 型チェックの自動実行

TypeScript ファイルを編集するたびに型チェックを実行するパターンです。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'if echo \"$CLAUDE_TOOL_OUTPUT_PATH\" | grep -q \"\\.tsx\\?$\"; then npx tsc --noEmit 2>&1 | tail -20; fi'"
          }
        ]
      }
    ]
  }
}
```

---

## 複数フックの組み合わせ

同じイベントに複数のフックを登録できます：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "npx prettier --write \"$CLAUDE_TOOL_OUTPUT_PATH\" 2>/dev/null || true" },
          { "type": "command", "command": "npx eslint --fix \"$CLAUDE_TOOL_OUTPUT_PATH\" 2>/dev/null || true" }
        ]
      }
    ],
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

## 次のステップ

→ [3-4: settings.json 設定](04-settings-config.md) に進む
