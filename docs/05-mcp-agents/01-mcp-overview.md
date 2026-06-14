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
Claude Code（MCP クライアント）
        │
        │ MCP プロトコル
        │ （stdio / SSE）
        │
        ▼
   MCP サーバー
        │
        ▼
   外部システム
   （GitHub / DB / Slack 等）
```

## MCP が提供する3種類の機能

### Tools（ツール）
Claude Code が呼び出せる関数。ファイル読み書き、API 呼び出しなど。

### Resources（リソース）
Claude Code がコンテキストとして読み込めるデータ。ドキュメント、DB レコードなど。

### Prompts（プロンプト）
再利用可能なプロンプトテンプレート。

## MCP vs 組み込みツール

| 観点 | 組み込みツール（Read/Edit/Bash 等） | MCP ツール |
|------|----------------------------------|----------|
| 目的 | ローカルファイル・シェル操作 | 外部サービス連携 |
| 設定 | 不要 | `settings.json` に記述 |
| 追加方法 | できない | MCP サーバーをインストール |
| 認証 | 不要 | API キー等が必要 |

## 公式 MCP サーバー一覧

[modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) で多数の公式サーバーが公開されています：

| サーバー | 用途 |
|---------|------|
| `@modelcontextprotocol/server-filesystem` | ファイルシステム操作 |
| `@modelcontextprotocol/server-github` | GitHub 操作 |
| `@modelcontextprotocol/server-sqlite` | SQLite DB 操作 |
| `@modelcontextprotocol/server-brave-search` | Web 検索 |
| `@modelcontextprotocol/server-slack` | Slack 操作 |
| `@modelcontextprotocol/server-postgres` | PostgreSQL 操作 |

## 次のステップ

→ [5-2: MCP サーバー設定](02-mcp-servers.md) に進む
