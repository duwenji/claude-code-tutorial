# Claude Code Tutorial — 完全実践ガイド

Anthropic の AI コーディングアシスタント **Claude Code** を基礎から応用まで体系的に学ぶチュートリアルです。

> 💡 ブラウザで https://duwenji.github.io/spa-quiz-app/ を開くと、関連トピックをクイズ形式で復習できます。

---

## 🎯 このチュートリアルの特徴

| 観点 | 内容 |
|------|------|
| **フォーカス** | Claude Code の全機能を体系的に習得 |
| **対象者** | 開発者（初心者〜上級者） |
| **実行環境** | Claude Code CLI / VS Code 拡張 / Desktop App |
| **サンプル** | 実践スキル（code-review / debug / test-gen）+ 設定テンプレート |
| **構成** | 6 Part・32セクション |
| **特徴** | スキル・フック・メモリ・MCP の4大機能を完全カバー |

---

## 📚 6層構造

```
基礎層  ── What is Claude Code / インストール / 基本概念
機能層  ── チャット・編集 / スラッシュコマンド / ターミナル / コンテキスト
スキル層 ── Skills システム / skill-creator / 組み込みスキル / カスタムスキル
自動化層 ── Hooks / 設定 / 自動化パターン
記憶層  ── メモリシステム / CLAUDE.md / 永続化
拡張層  ── MCP / エージェントSDK / マルチエージェント / CI/CD
```

---

## 📖 学習の流れ

### Part 0: 基礎と環境準備（20分）⭐
| # | セクション | 内容 |
|---|-----------|------|
| 0-1 | Claude Code とは | 設計思想、アーキテクチャ、他ツールとの比較 |
| 0-2 | インストールとセットアップ | インストール方法、初期設定、IDE 連携 |

### Part 1: コア機能（60分）
| # | セクション | 内容 |
|---|-----------|------|
| 1-1 | チャットとファイル編集 | 基本的なコード生成・編集・リファクタリング |
| 1-2 | スラッシュコマンド一覧 | /help, /clear, /code-review, /run 等の使い方 |
| 1-3 | ターミナル・Bash ツール | シェルコマンド実行、パイプライン、権限管理 |
| 1-4 | コンテキスト管理 | ファイル・フォルダ読み込み、コンテキスト圧縮 |

### Part 2: スキルシステム（80分）⭐
| # | セクション | 内容 |
|---|-----------|------|
| 2-1 | スキルとは | SKILL.md の構造、自動読み込みの仕組み |
| 2-2 | /skill-creator ハンズオン | 対話的スキル生成、テスト・評価・改善サイクル |
| 2-3 | 組み込みスキル活用 | /code-review, /debug, /run, /security-review |
| 2-4 | カスタムスキル作成 | SKILL.md 手書き、best practices |

### Part 3: フックと自動化（60分）
| # | セクション | 内容 |
|---|-----------|------|
| 3-1 | フックの概要 | hooks システムの仕組み、イベント一覧 |
| 3-2 | フックの種類 | PreToolUse / PostToolUse / Stop / Notification |
| 3-3 | 自動化パターン | lint自動実行、テスト実行、通知連携 |
| 3-4 | settings.json 設定 | 権限管理、環境変数、モデル選択 |

### Part 4: メモリシステム（60分）
| # | セクション | 内容 |
|---|-----------|------|
| 4-1 | メモリの種類 | user / feedback / project / reference |
| 4-2 | CLAUDE.md ファイル | プロジェクト指示書の書き方 |
| 4-3 | 永続的なメモリ | ファイルベースメモリ、セッション間の継続性 |
| 4-4 | メモリのベストプラクティス | 効果的な記憶管理、ノイズを減らすコツ |

### Part 5: MCP とエージェント（90分）🆕
| # | セクション | 内容 |
|---|-----------|------|
| 5-1 | MCP の概要 | Model Context Protocol の仕組みと目的 |
| 5-2 | MCP サーバー設定 | stdio / SSE サーバー、認証、設定方法 |
| 5-3 | エージェント SDK | カスタムエージェント開発の基礎 |
| 5-4 | マルチエージェント | 並列実行、サブエージェント、オーケストレーション |
| 5-5 | カスタムエージェント実装 | 専門エージェントの設計と実装 |

### Part 6: 発展・応用（65分）
| # | セクション | 内容 |
|---|-----------|------|
| 6-1 | CI/CD 統合 | GitHub Actions, GitLab CI への組み込み |
| 6-2 | チームワークフロー | 共有設定、権限管理、レビュー自動化 |
| 6-3 | パフォーマンス Tips | キャッシング、プロンプト最適化 |
| 6-4 | トラブルシューティング | よくあるエラーと解決策 |

---

## ⏱ 学習時間目安

| Part | セクション数 | 学習時間 | 難易度 |
|------|------------|---------|-------|
| Part 0 | 2 | 20分 | ⭐ |
| Part 1 | 4 | 60分 | ⭐⭐ |
| Part 2 | 4 | 80分 | ⭐⭐ |
| Part 3 | 4 | 60分 | ⭐⭐ |
| Part 4 | 4 | 60分 | ⭐⭐ |
| Part 5 | 5 | 90分 | ⭐⭐⭐ |
| Part 6 | 4 | 65分 | ⭐⭐⭐ |
| **合計** | **27** | **7時間35分** | |

---

## 🚀 クイックスタート（5分）

### 1. Claude Code をインストールする

**推奨（ネイティブインストーラー）**

```bash
# macOS / Linux / WSL
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

**その他の方法**

```bash
# Homebrew (macOS)
brew install --cask claude-code

# WinGet (Windows)
winget install Anthropic.ClaudeCode
```

> ⚠️ `npm install -g @anthropic-ai/claude-code` は**非推奨**になりました。
> ネイティブインストーラーは自動更新に対応しています。

```bash
# バージョン確認
claude --version
```

### 2. 最初の会話を始める
```bash
# プロジェクトディレクトリで起動
cd my-project
claude

# ファイルを読んでもらう
> このプロジェクトの構造を説明して
```

### 3. スキルを使う
```bash
# 組み込みコードレビュースキルを実行
/code-review

# デバッグスキルを実行
/debug
```

---

## 📁 ファイル構成

```
claude-code-tutorial/
├── README.md
├── CHANGELOG.md
├── MASTER-INDEX.md
├── QUICK-REFERENCE.md
├── ROADMAP.md
├── CONTRIBUTING.md
├── LICENSE
├── docs/
│   ├── 00-fundamentals/          # Part 0: 基礎・環境準備
│   ├── 01-core-features/         # Part 1: コア機能
│   ├── 02-skills/                # Part 2: スキルシステム ⭐
│   ├── 03-hooks-automation/      # Part 3: フックと自動化
│   ├── 04-memory-system/         # Part 4: メモリシステム
│   ├── 05-mcp-agents/            # Part 5: MCP・エージェント 🆕
│   └── 06-advanced/              # Part 6: 発展・応用
├── samples/                      # 実装サンプル（SKILL.md）
│   ├── code-review/
│   ├── debug-assistant/
│   └── test-generator/
├── assets/                       # 図表類
│   └── diagrams/
└── ebook-output/                 # 電子書籍出力
```

---

## 🎓 学習後、あなたができること

✓ Claude Code を使ってコードの生成・編集・レビューができる  
✓ スラッシュコマンドとスキルを使いこなせる  
✓ Hooks で繰り返し作業を自動化できる  
✓ CLAUDE.md と永続メモリでコンテキストを管理できる  
✓ MCP サーバーを接続して外部ツールと連携できる  
✓ カスタムエージェントを設計・実装できる  
✓ CI/CD パイプラインに Claude Code を組み込める

---

## 🔗 関連リソース

### 公式ドキュメント

| リソース | URL |
|---------|-----|
| Claude Code 公式ドキュメント | https://docs.anthropic.com/claude-code |
| Anthropic API リファレンス | https://docs.anthropic.com/api |
| Agent Skills 標準 | https://agentskills.io |
| MCP 仕様 | https://modelcontextprotocol.io |

### GitHub リポジトリ

- [anthropics/claude-code](https://github.com/anthropics/claude-code) — Claude Code 本体
- [anthropics/skills](https://github.com/anthropics/skills) — 公式スキルコレクション
- [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) — MCP サーバー一覧

---

## 🤝 貢献・フィードバック

改善提案、誤字修正、新しいセクション提案は Issue/PR でお気軽にお寄せください。

## 📄 ライセンス

MIT License - 自由に使用、改変、配布できます。

---

**最終更新**: 2026年6月14日  
**バージョン**: 0.1.0 🚧 初版作成中

👉 [Part 0: Claude Code とは →](docs/00-fundamentals/01-what-is-claude-code.md)
