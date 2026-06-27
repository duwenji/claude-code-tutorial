# 6-4: トラブルシューティング

> **学習時間**: 15分 | **難易度**: ⭐⭐⭐

## まず試すこと: `/doctor`

問題が発生したらまずこのコマンドを実行します：

```
/doctor
```

または、Claude Code が起動しない場合はシェルから：

```bash
claude doctor
```

インストール状態・設定ファイルの妥当性・MCP サーバーの接続・コンテキスト使用量を自動チェックします。

## 問題別 クイックリファレンス

| 症状 | 参照セクション |
|------|-------------|
| `command not found: claude` | [起動しない](#claude-code-が起動しない) |
| `ANTHROPIC_API_KEY` エラー | [認証エラー](#認証エラー) |
| ツールが承認待ちになり続ける | [ツール承認](#ツールが承認待ちになり続ける) |
| スキルが自動読み込みされない | [スキル](#スキルが自動読み込みされない) |
| MCP サーバーが接続できない | [MCP](#mcp-サーバーが接続できない) |
| コンテキストが切れてしまう | [コンテキスト](#コンテキストが切れてしまう) |
| フックが動作しない | [フック](#フックが動作しない) |
| レート制限エラー | [API エラー](#api-レート制限エラー) |
| Windows 文字化け | [Windows](#windows-での文字化け) |
| WSL での検索問題 | [WSL](#wsl-での検索パフォーマンス) |
| 応答が遅い・ハング | [パフォーマンス](#高-cpu--メモリ使用率とハング) |

---

## よくある問題と解決策

### Claude Code が起動しない

**症状**: `claude` コマンドが見つからない

```bash
# 確認
which claude       # macOS/Linux
claude --version   # インストール確認
claude doctor      # 自動診断
```

**PATH が通っていない場合**:

```bash
# macOS/Linux (Zsh)
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# macOS/Linux (Bash)
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

```powershell
# Windows PowerShell
$currentPath = [Environment]::GetEnvironmentVariable('PATH', 'User')
[Environment]::SetEnvironmentVariable('PATH', "$currentPath;$env:USERPROFILE\.local\bin", 'User')
# ターミナルを再起動
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

---

### 認証エラー

**症状**: `ANTHROPIC_API_KEY` が未設定、または `403 Forbidden`

```bash
# API キーを確認
echo $ANTHROPIC_API_KEY

# 設定
export ANTHROPIC_API_KEY="sk-ant-..."

# 永続化（~/.bashrc または ~/.zshrc に追加）
echo 'export ANTHROPIC_API_KEY="sk-ant-..."' >> ~/.bashrc
```

**症状**: `403 Forbidden` でログイン済みなのに動かない

原因の可能性:
1. `ANTHROPIC_API_KEY` 環境変数が残っており、OAuth を上書きしている
   - `unset ANTHROPIC_API_KEY` で一時的に解除してから `claude` を起動
   - `~/.bashrc` や `~/.zshrc` から `export ANTHROPIC_API_KEY=...` を削除
2. Claude Pro/Max サブスクリプションの有効期限切れ
3. Anthropic Console でのロール設定不足（"Claude Code" または "Developer" ロールが必要）

**症状**: OAuth エラー（WSL2・SSH・コンテナ環境）

ブラウザが別のホストで開き、リダイレクトが Claude Code に届かない場合：
- ターミナルに表示されるログインコードを `Paste code here if prompted` プロンプトに貼り付ける
- `c` を押して OAuth URL をコピーし、ローカルのブラウザで開く

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

**検索が機能しない場合**（ファイル検索・`@file` が動かない）:

バンドルされた `ripgrep` が動作していない可能性があります。プラットフォーム固有のものをインストールして Claude Code に使わせます：

```bash
# macOS
brew install ripgrep

# Ubuntu/Debian
sudo apt install ripgrep

# Windows
winget install BurntSushi.ripgrep.MSVC
```

その後、`settings.json` に追加：

```json
{
  "env": {
    "USE_BUILTIN_RIPGREP": "0"
  }
}
```

---

### コンテキストが切れてしまう

**症状**: 途中で会話がリセットされる、過去の指示を忘れる

**解決策**:
1. 重要な指示は CLAUDE.md に書く
2. メモリに保存する（`> これを覚えておいて`）
3. `/compact` でコンテキストを圧縮しながら会話を継続する
4. 長いタスクは小さく分割して `/clear` を挟む

**自動圧縮がスラッシングする場合**（`Autocompact is thrashing` エラー）:

巨大なファイルや出力がコンテキストをすぐ埋めてしまう場合：
1. ファイルを行範囲で指定して読む（全体を一度に読まない）
2. `/compact keep only the plan and the diff` のようにフォーカスを指定
3. 大きなファイル処理はサブエージェントに委譲

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

**`--safe-mode` で切り分け**:

```bash
claude --safe-mode
```

プラグイン・MCP サーバー・フックを全て無効化した状態で起動します。これで問題が消えれば設定ファイルに原因があります。

---

### API レート制限エラー

**症状**: `rate_limit_error`（429）や `529 Overloaded` が発生する

**解決策**:
- より軽量なモデルを使う（`/model claude-haiku-4-5`）
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

**Windows での Path 区切り文字**:

Claude Code は Windows でも基本的にスラッシュ（`/`）を使います。PowerShell のコマンドではバックスラッシュ（`\`）が必要な場合があります。

---

### WSL での検索パフォーマンス

**症状**: ファイル検索が遅い・結果が少ない

WSL でクロスファイルシステム（`/mnt/c/` 配下）で作業すると、ファイル読み込みのペナルティで検索結果が少なくなることがあります。

**対処法**:
1. **検索を絞り込む**: ディレクトリやファイル種別を指定する（「`auth-service` パッケージで JWT 検証ロジックを検索して」）
2. **Linux ファイルシステムに移動**: プロジェクトを `/home/` 配下に置く
3. **ネイティブ Windows で実行**: WSL 経由でなく Windows 上で直接動かす

---

### 高 CPU / メモリ使用率とハング

**症状**: Claude Code が重い、または応答しない

**対処法**:
1. `/compact` でコンテキストを圧縮
2. 大きなビルドディレクトリを `.gitignore` に追加
3. `claude --safe-mode` で起動して設定の影響を切り分け
4. `/heapdump` でメモリスナップショットを取得（Linux/macOS はホームディレクトリに保存）
5. ハングした場合: `Ctrl+C` で中断。それでも応答しなければターミナルを閉じる

**会話の再開**（再起動後も続きから始められます）:

```bash
claude --resume
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

---

## サポートリソース

| リソース | URL / コマンド |
|---------|--------------|
| 公式ドキュメント | https://code.claude.com/docs/ |
| GitHub Issues | https://github.com/anthropics/claude-code/issues |
| ヘルプ | `/help` コマンドで確認 |
| フィードバック送信 | `/feedback` コマンドで直接報告 |
| 自動診断 | `claude doctor` / `/doctor` |

## 🏋️ 練習問題

1. **【確認】** `claude doctor`（または `/doctor`）で確認できる診断項目を3つ答えてください。また、Claude Code が起動しない場合と起動している場合でどちらのコマンドを使うべきか説明してください。

2. **【実践】** 意図的に `ANTHROPIC_API_KEY` を空にして Claude Code を起動し、表示されるエラーメッセージを確認しましょう（確認後は必ず元に戻すこと）。エラーメッセージにはどのような情報が含まれていましたか？

3. **【実践】** Claude Code が応答しなくなった場合の対処手順（`Ctrl+C` → ターミナル再起動 → `claude --resume`）を実際に試してみましょう。`--resume` で会話がどこまで復元されるか確認してください。

4. **【応用】** チームメンバーから「Claude Code が動かない」と言われた場合の診断フローを3ステップで説明してください。`/doctor` 、エラーメッセージの確認、`--safe-mode` を活用した切り分けを含めてください。
