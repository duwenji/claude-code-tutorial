# 6-4: トラブルシューティング

> **学習時間**: 15分 | **難易度**: ⭐⭐⭐

## よくある問題と解決策

### Claude Code が起動しない

**症状**: `claude` コマンドが見つからない

```bash
# 確認
which claude
npm list -g @anthropic-ai/claude-code

# 再インストール
npm install -g @anthropic-ai/claude-code
```

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
- Node.js のバージョンが古い（18 以上が必要）

---

### コンテキストが切れてしまう

**症状**: 途中で会話がリセットされる、過去の指示を忘れる

**解決策**:
1. 重要な指示は CLAUDE.md に書く
2. メモリに保存する（`> これを覚えておいて`）
3. 長いタスクは小さく分割して `/clear` を挟む

---

### フックが動作しない

**症状**: `PostToolUse` フックが実行されない

**デバッグ方法**:

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

**症状**: 日本語が文字化けする

```bash
# PowerShell のエンコーディングを設定
$OutputEncoding = [System.Text.Encoding]::UTF8
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
```

---

## デバッグモード

詳細なログを出力するには：

```bash
CLAUDE_DEBUG=true claude
```

---

## サポートリソース

| リソース | URL |
|---------|-----|
| 公式ドキュメント | https://docs.anthropic.com/claude-code |
| GitHub Issues | https://github.com/anthropics/claude-code/issues |
| Claude Code ガイド | `/help` コマンドで確認 |
