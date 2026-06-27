# 3-3: 自動化パターン

> **学習時間**: 15分 | **難易度**: ⭐⭐

## パターン1: コード品質の自動維持

ファイルを編集するたびに lint と format を自動実行するパターンです。

ツールの入力は **stdin から JSON** で受け取ります。`jq` でファイルパスを取り出して使います：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); npx eslint --fix \"$FILE\" 2>/dev/null; npx prettier --write \"$FILE\" 2>/dev/null; true'"
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
# stdin から JSON でファイルパスを取得
CHANGED_FILE=$(cat | jq -r '.tool_input.file_path // empty')
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
# stdin から JSON でコマンドを取得
INPUT=$(cat | jq -r '.tool_input.command // empty')

# force push をブロック
if echo "$INPUT" | grep -q "push.*--force\|push.*-f"; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: "force push は禁止されています。レビューを経てください。"
    }
  }'
  exit 0
fi

# main への直接 push をブロック
if echo "$INPUT" | grep -qE "push.*origin main|push.*origin master"; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: "main への直接 push は禁止されています。PR を作成してください。"
    }
  }'
  exit 0
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
            "command": "bash -c 'CMD=$(cat | jq -r .tool_input.command); echo \"[$(date +%Y-%m-%d\\ %H:%M:%S)] CMD: $CMD\" >> ~/.claude/activity.log'"
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
            "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); if echo \"$FILE\" | grep -q \"\\.tsx\\?$\"; then npx tsc --noEmit 2>&1 | tail -20; fi'"
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
          { "type": "command", "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); npx prettier --write \"$FILE\" 2>/dev/null || true'" },
          { "type": "command", "command": "bash -c 'FILE=$(cat | jq -r .tool_input.file_path); npx eslint --fix \"$FILE\" 2>/dev/null || true'" }
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

## 🏋️ 練習問題

1. **【確認】** フックを使って lint エラーがある場合にファイル編集をブロックするには、どのフックタイプを使いますか？
2. **【実践】** ファイル編集後に `echo "編集完了: $FILE"` を出力するフックを設定してみましょう（`FILE` は stdin JSON から `jq` で取得します）。
3. **【実践】** Claude が完了した際に OS 通知（macOS: `osascript`、Windows: `powershell -command "[System.Console]::Beep(1000, 300)"`）を送るフックを作成しましょう。
4. **【応用】** CI/CD 環境でフックを使う場合の注意点を2つ挙げてください。

## 次のステップ

→ [3-4: settings.json 設定](04-settings-config.md) に進む
