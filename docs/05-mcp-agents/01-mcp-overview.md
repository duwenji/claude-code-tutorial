# 5-1: MCP の概要

> **学習時間**: 15分 | **難易度**: ⭐⭐⭐

## MCP（Model Context Protocol）とは

MCP は Anthropic が策定したオープン標準プロトコルです。AI モデルと外部ツール・データソースを安全に接続するための共通インターフェースを定義しています。

Claude Code は MCP クライアントとして動作し、MCP サーバーを通じて外部システムと連携できます。

## MCP でできること

| カテゴリ | 例 |
|---------|-----|
| ファイルシステム | 指定ディレクトリの読み書き |
| データベース | SQLite / PostgreSQL クエリ |
| バージョン管理 | GitHub Issue / PR 操作 |
| Web | URL フェッチ、検索 |
| コミュニケーション | Slack メッセージ送信 |
| クラウド | AWS / GCP / Azure 操作 |
| 開発ツール | Jira / Linear / Notion 操作 |

## MCP のアーキテクチャ

```
Claude Code（MCP ホスト）
        │
        │ MCP クライアント（サーバーごとに1つ）
        │
        ├── MCP サーバー A（ローカル: stdio）
        │        │
        │        ▼
        │   ローカルツール・DB 等
        │
        └── MCP サーバー B（リモート: Streamable HTTP）
                 │
                 ▼
           クラウドサービス（GitHub / Slack 等）
```

MCP は2層で構成されています：

- **データ層**: JSON-RPC 2.0 ベースのプロトコル。Tools / Resources / Prompts などのプリミティブを定義
- **トランスポート層**: 通信チャネル。stdio（ローカル）と Streamable HTTP（リモート）の2種類

### トランスポートの種類

| トランスポート | 用途 | 特徴 |
|-------------|------|------|
| **stdio** | ローカルプロセス間通信 | 低レイテンシ、ネットワーク不要 |
| **Streamable HTTP** | リモートサーバー接続 | OAuth 2.0 認証対応、クラウドサービス向け |
| SSE（非推奨） | リモートサーバー接続 | 後方互換のために残存、新規利用は非推奨 |

## MCP が提供する3種類の機能

### Tools（ツール）
Claude Code が呼び出せる関数。ファイル読み書き、API 呼び出しなど。

### Resources（リソース）
Claude Code がコンテキストとして読み込めるデータ。ドキュメント、DB レコードなど。
`@サーバー名:プロトコル://パス` の形式でプロンプト内から参照できます。

```
@github:issue://123 の内容を確認して修正案を提案して
```

### Prompts（プロンプト）
再利用可能なプロンプトテンプレート。`/mcp__サーバー名__プロンプト名` の形式でスラッシュコマンドとして実行できます。

## MCP vs 組み込みツール

| 観点 | 組み込みツール（Read/Edit/Bash 等） | MCP ツール |
|------|----------------------------------|----------|
| 目的 | ローカルファイル・シェル操作 | 外部サービス連携 |
| 設定 | 不要 | `claude mcp add` コマンドまたは設定ファイル |
| 追加方法 | できない | MCP サーバーをインストール |
| 認証 | 不要 | API キー・OAuth 等が必要 |

## 公式 MCP サーバー一覧

[modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) や [Anthropic Directory](https://claude.ai/directory) で多数の公式サーバーが公開されています：

| サーバー | 用途 |
|---------|------|
| `@modelcontextprotocol/server-filesystem` | ファイルシステム操作 |
| `@modelcontextprotocol/server-github` | GitHub 操作 |
| `@modelcontextprotocol/server-sqlite` | SQLite DB 操作 |
| `@modelcontextprotocol/server-brave-search` | Web 検索 |
| `@modelcontextprotocol/server-slack` | Slack 操作 |
| `@modelcontextprotocol/server-postgres` | PostgreSQL 操作 |

Anthropic Directory に登録されているリモートサーバーは `claude mcp add` コマンドで直接追加できます。

## 🏋️ 練習問題

1. **【確認】** MCP が提供する3種類の機能（Tools / Resources / Prompts）をそれぞれ説明してください。
2. **【確認】** 組み込みツール（Read/Edit/Bash）と MCP ツールの主な違いを2つ答えてください。
3. **【実践】** 公式の MCP サーバー一覧（modelcontextprotocol/servers または Anthropic Directory）を確認し、自分のプロジェクトで使えそうなサーバーを1つ選んでみましょう。
4. **【応用】** GitHub MCP サーバーを使うことで、現在の作業フローのどの部分を改善できますか？

## 次のステップ

→ [5-2: MCP サーバー設定](02-mcp-servers.md) に進む
