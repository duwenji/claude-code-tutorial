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

**WinGet（Windows）:**

```powershell
winget install Anthropic.ClaudeCode
```

**npm（Node.js 18+ が必要）:**

```bash
npm install -g @anthropic-ai/claude-code
```

> Homebrew・WinGet・npm 経由のインストールは自動更新されません。手動で `brew upgrade claude-code` / `winget upgrade Anthropic.ClaudeCode` / `npm install -g @anthropic-ai/claude-code@latest` を実行して更新してください。

## インストール確認

```bash
claude --version

# より詳細な診断
claude doctor
```

## 認証設定

### 方法1: Claude.ai アカウント（推奨・通常の使い方）

初回起動時にブラウザが開き、Claude アカウントでサインインします。

```bash
claude auth login
```

対応プランと特徴：

| プラン | 月額 | Claude Code での利用 |
|--------|------|---------------------|
| Max | $100/$200 | レート制限が高く最適 |
| Pro | $20 | 利用可能（ヘビーな使用時に制限あり） |
| Team | $25/人 | チーム向け、管理機能あり |
| Enterprise | 要相談 | SSO・管理コンソール対応 |

その他の認証コマンド：

```bash
claude auth logout    # ログアウト
claude auth status    # 認証状態を確認
claude setup-token    # CI 用の長期トークンを生成
```

### 方法2: Anthropic Console / API キー

Amazon Bedrock・Google Vertex AI など外部プロバイダー経由、または API キーで利用する場合：

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

## モデルの選択

`settings.json` でモデルを指定できます：

```json
{
  "model": "claude-opus-4-8"
}
```

利用可能なモデル：

| モデル ID | 特徴 |
|----------|------|
| `claude-opus-4-8` | 最高性能、複雑なタスク向け |
| `claude-sonnet-4-6` | バランス型（デフォルト） |
| `claude-haiku-4-5-20251001` | 高速・軽量 |

## 動作確認

```bash
# インストールと設定の診断
claude doctor

# 起動して動作確認
claude
> Hello! このプロジェクトの README.md を読んで概要を教えて
```

`claude doctor` はインストール状態・認証・ネットワーク接続などを診断し、問題があれば修正方法を提示します。Claude Code がファイルを読み込み、プロジェクトの概要を説明します。

## 次のステップ

→ [1-1: チャットとファイル編集](../01-core-features/01-chat-and-editing.md) に進む
