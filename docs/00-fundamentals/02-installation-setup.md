# 0-2: インストールとセットアップ

> **学習時間**: 10分 | **難易度**: ⭐

## 前提条件

### システム要件

| 項目 | 要件 |
|------|------|
| OS | macOS 13.0+、Windows 10 1809+、Ubuntu 20.04+、Debian 10+、Alpine 3.19+ |
| RAM | 4GB 以上 |
| ネットワーク | インターネット接続必須 |
| Node.js | npm 経由でインストールする場合のみ 18 以上が必要 |

### アカウント要件

Claude.ai の有料プラン（Pro / Max / Team / Enterprise）または Anthropic Console アカウントが必要です。**無料プランは Claude Code を利用できません。**

## インストール

### 推奨: ネイティブインストーラー

ネイティブインストーラーは Node.js 不要で、バックグラウンドで自動更新されます。

**macOS / Linux / WSL:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD:**

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

### その他のインストール方法

**Homebrew（macOS）:**

```bash
brew install --cask claude-code
```

> `claude-code` は安定版チャネル（約1週間遅れ）、`claude-code@latest` は最新チャネルを追跡します。

**WinGet（Windows）:**

```powershell
winget install Anthropic.ClaudeCode
```

**Linux パッケージマネージャー（apt / dnf / apk）:**

Debian/Ubuntu（apt）、Fedora/RHEL（dnf）、Alpine Linux（apk）でもリポジトリ経由でインストールできます。詳細は[公式ドキュメント](https://code.claude.com/docs/ja/setup)を参照してください。

**npm（Node.js 18+ が必要）:**

```bash
npm install -g @anthropic-ai/claude-code
```

> **更新方法の違い**: ネイティブインストールはバックグラウンドで自動更新されます。Homebrew・WinGet・Linux パッケージマネージャー経由のインストールは自動更新されないため、手動で `brew upgrade claude-code` / `winget upgrade Anthropic.ClaudeCode` / `npm install -g @anthropic-ai/claude-code@latest` を実行して更新してください。npm インストールは Claude Code 内部で自動更新されますが、npm の書き込み権限がない場合は手動更新が必要です。

## インストール確認

```bash
claude --version

# より詳細な診断（インストール状態・認証・ネットワーク接続を確認）
claude doctor
```

手動で最新版に更新するには：

```bash
claude update
```

## 認証設定

### 方法1: Claude.ai アカウント（推奨・通常の使い方）

初回起動時に `claude` を実行するとブラウザが開き、Claude アカウントでサインインできます。

```bash
claude
# → 初回起動時に認証を求めるプロンプトが表示される
```

対応プランと特徴：

| プラン | 月額（目安） | Claude Code での利用 |
|--------|------|---------------------|
| Max | $100/$200 | レート制限が高く最適 |
| Pro | $20 | 利用可能（ヘビーな使用時に制限あり） |
| Team | $25/人 | チーム向け、管理機能あり |
| Enterprise | 要相談 | SSO・管理コンソール対応 |

> **注意**: 無料の Claude.ai プランは Claude Code を利用できません。

その他の認証コマンド：

```bash
claude auth logout    # ログアウト
claude auth status    # 認証状態を確認
```

### 方法2: Anthropic Console / API キー・サードパーティプロバイダー

Amazon Bedrock・Google Vertex AI・Microsoft Foundry など外部プロバイダー経由、または API キーで利用する場合：

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

永続的に設定するには `~/.bashrc` / `~/.zshrc` に追加します。

> **注意**: `ANTHROPIC_API_KEY` が設定されている場合、サブスクリプションよりAPIキーが優先されます。

## 初回起動

プロジェクトのルートディレクトリで起動します：

```bash
cd my-project
claude
```

初回起動時に認証方法の選択など、いくつかの設定確認が行われます。

## IDE 拡張のインストール

### VS Code

1. VS Code を開く
2. 拡張機能（Ctrl+Shift+X）を開く
3. 「Claude Code」を検索してインストール
4. コマンドパレット（Ctrl+Shift+P）から `Claude Code: Open` を実行

### JetBrains

1. Settings → Plugins → Marketplace
2. 「Claude Code」を検索してインストール

## CLAUDE.md の初期化

プロジェクトルートで `CLAUDE.md` を自動生成できます：

```bash
claude
/init
```

Claude Code がプロジェクトを分析し、CLAUDE.md の初稿を作成します。

## 設定ファイルの場所

| ファイル | 場所 | 用途 |
|---------|------|------|
| グローバル設定 | `~/.claude/settings.json` | 全プロジェクト共通設定 |
| プロジェクト設定 | `.claude/settings.json` | プロジェクト固有設定 |
| プロジェクト指示 | `CLAUDE.md` | プロジェクトのコンテキスト |
| 個人用スキル | `~/.claude/skills/` | 全プロジェクトで使えるスキル |
| プロジェクト用スキル | `.claude/skills/` | そのプロジェクト専用スキル |
| メモリ | `~/.claude/projects/<hash>/memory/` | プロジェクト別の永続メモリ |
| MCP 設定 | `.mcp.json` | プロジェクトの MCP サーバー設定 |

## モデルの選択

`settings.json` でモデルを指定できます：

```json
{
  "model": "claude-sonnet-4-6"
}
```

利用可能なモデル（エイリアスまたはフル ID で指定）：

| モデルエイリアス | フル ID 例 | 特徴 |
|----------|------|------|
| `opus` | `claude-opus-4-6` | 最高性能、複雑なタスク向け |
| `sonnet` | `claude-sonnet-4-6` | バランス型（デフォルト） |
| `haiku` | `claude-haiku-4-5` | 高速・軽量 |

> エイリアス（`"opus"`・`"sonnet"`・`"haiku"`）を使うと、同じ世代の最新モデルへ自動的に追従されます。`/model` コマンドやセッション内で切り替えることもできます。

## 動作確認

```bash
# インストールと設定の診断
claude doctor

# 起動して動作確認
claude
> Hello! このプロジェクトの README.md を読んで概要を教えて
```

`claude doctor` はインストール状態・認証・ネットワーク接続などを診断し、問題があれば修正方法を提示します。Claude Code がファイルを読み込み、プロジェクトの概要を説明します。

## 🏋️ 練習問題

1. **【確認】** ネイティブインストーラーと npm インストールの違いを1つ答えてください。
2. **【実践】** `claude --version` と `claude doctor` を実行し、バージョンと診断結果を確認しましょう。
3. **【実践】** プロジェクトディレクトリで `claude` を起動し、`/init` で CLAUDE.md を生成してみましょう。
4. **【応用】** チームで Claude Code を導入する場合、API キー認証と claude.ai プラン認証のどちらが適切ですか？理由とともに答えてください。

## 次のステップ

→ [1-1: チャットとファイル編集](../01-core-features/01-chat-and-editing.md) に進む
