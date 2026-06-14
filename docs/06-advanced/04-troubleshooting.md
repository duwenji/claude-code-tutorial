# 6-4: トラブルシューティング

> **学習時間**: 15分 | **難易度**: ⭐⭐⭐

## よくある問題と解決策

### Claude Code が起動しない

**症状**: `claude` コマンドが見つからない

```bash
# 確認
which claude       # macOS/Linux
claude --version   # インストール確認
claude doctor      # 自動診断
```

**再インストール**（ネイティブインストーラーが推奨）:

```bash
# macOS/Linux
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# Homebrew (macOS)
brew install --cask claude-code

# WinGet (Windows)
winget install Anthropic.ClaudeCode
```

インストール先: `~/.local/bin/claude`（macOS/Linux）/ `%USERPROFILE%\.local\bin\claude.exe`（Windows）

**症状**: `ANTHROPIC_API_KEY` が未設定

```bash
# 確認
echo $ANTHROPIC_API_KEY

# 設定
export ANTHROPIC_API_KEY="sk-ant-..."

# 永続化（~/.bashrc または ~/.zshrc に追加）
echo 'export ANTHROPIC_API_KEY="sk-ant-..."' >> ~/.bashrc
```

---

### ツールが承認待ちになり続ける

**症状**: 毎回 `Bash(git status)` の承認を求められる

**解決策**: `settings.json` に許可ルールを追加する

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm *)",
      "Read(**)",
      "Edit(**)"
    ]
  }
}
```

---

### スキルが自動読み込みされない

**症状**: `.claude/skills/my-skill/SKILL.md` を置いたが自動起動しない

**確認ポイント**:
1. `description` フィールドが具体的か確認する
2. ディレクトリ名とファイル名が正しいか確認する

```bash
# 構造を確認
ls .claude/skills/my-skill/
# → SKILL.md が存在すること

# description を確認
head -10 .claude/skills/my-skill/SKILL.md
# → description フィールドが具体的に書かれていること
```

スラッシュコマンドで明示的に呼び出すこともできます：
```
/my-skill
```

---

### MCP サーバーが接続できない

**症状**: MCP ツールが使えない

```bash
# MCP サーバーを手動で起動してテスト
npx -y @modelcontextprotocol/server-filesystem /tmp

# settings.json の記述を確認
cat ~/.claude/settings.json | grep -A 10 mcpServers
```

**よくある原因**:
- `npx` のパスが通っていない
- 環境変数（API キー等）が未設定
- Node.js のバージョンが古い（npm 経由インストールの場合は 18 以上が必要）

---

### コンテキストが切れてしまう

**症状**: 途中で会話がリセットされる、過去の指示を忘れる

**解決策**:
1. 重要な指示は CLAUDE.md に書く
2. メモリに保存する（`> これを覚えておいて`）
3. `/compact` でコンテキストを圧縮しながら会話を継続する
4. 長いタスクは小さく分割して `/clear` を挟む

---

### フックが動作しない

**症状**: `PostToolUse` フックが実行されない

**まず試すこと**:
```
/doctor
```
インストール・設定・MCP・コンテキストの状態を自動チェックしてくれる。

**ログを使ったデバッグ方法**:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'hook triggered' >> /tmp/claude-hooks.log"
          }
        ]
      }
    ]
  }
}
```

```bash
# ログを確認
tail -f /tmp/claude-hooks.log
```

---

### API レート制限エラー

**症状**: `rate_limit_error` が発生する

**解決策**:
- より軽量なモデルを使う
- `maxTurns` を制限する
- Anthropic Console でレート制限を確認・引き上げ申請

---

### Windows での文字化け

**症状**: 日本語が文字化けする、文字が豆腐（□）になる

**VS Code / Cursor の統合ターミナルで発生する場合**:

セッション内で以下を実行すると GPU レンダラーが無効化される：
```
/terminal-setup
```

**PowerShell のエンコーディング設定**（一般的な文字化けの場合）:

```powershell
$OutputEncoding = [System.Text.Encoding]::UTF8
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

---

## デバッグモード

詳細なログを出力するには：

```bash
# コマンドラインフラグ（推奨）
claude --debug

# 環境変数
DEBUG=1 claude
```

セッション起動後は `/debug` コマンドでも切り替え可能。

診断レポートを確認したい場合は：

```bash
claude doctor
```

---

## サポートリソース

| リソース | URL |
|---------|-----|
| 公式ドキュメント | https://code.claude.com/docs/ |
| GitHub Issues | https://github.com/anthropics/claude-code/issues |
| Claude Code ガイド | `/help` コマンドで確認 |
| フィードバック送信 | `/feedback` コマンドで直接報告 |
