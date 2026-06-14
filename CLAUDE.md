# CLAUDE.md

このファイルは、リポジトリ内のコードを操作する際に Claude Code (claude.ai/code) へのガイダンスを提供します。

## 概要

Claude Code の解説チュートリアルを目的としたドキュメント専用リポジトリです。全文が日本語で書かれており、ビルドシステム・パッケージマネージャー・実行可能コードは一切ありません。すべてのコンテンツは Markdown ファイルです。

## リポジトリ構造

```
docs/<part-number>-<topic>/   # チュートリアル本文（Part 00〜06 の7部構成）
samples/<skill-name>/SKILL.md # スキル実装サンプル
QUICK-REFERENCE.md            # 全主要機能のチートシート
MASTER-INDEX.md               # 全セクションへのナビゲーションインデックス
```

チュートリアルは6層で構成されています：基礎 → コア機能 → スキル → フック・自動化 → メモリシステム → MCP・エージェント → 発展・応用

## ドキュメント規約（CONTRIBUTING.md より）

- 本文はすべて**日本語**で記述する
- ファイル命名：`docs/<part>/<number>-<kebab-case>.md`（例：`docs/01-core-features/02-slash-commands.md`）
- 各セクションの冒頭に学習時間の目安と難易度を記載する
- コードブロックには言語を指定する（`bash`、`json`、`typescript` など）
- 各セクションの末尾に次のステップへのリンクを付ける

## SKILL.md フォーマット

`samples/` 内のサンプルスキルは以下のフロントマター構造に従っています：

```markdown
---
id: skill-id
name: スキル名
description: >
  1段落の説明。Claude が自動提案するトリガーキーワードを含める。
tags:
  - tag1
  - tag2
---
```

本文には「概要」「入力パラメータ表」「番号付き手順」「出力形式」「テストケース表」を定義します。

## 主要参照ファイル

- `QUICK-REFERENCE.md` — スラッシュコマンド・フックイベント・`settings.json` スキーマ・MCP 設定・SKILL.md テンプレートのチートシート
- `ROADMAP.md` — 今後追加予定のセクションとバージョン履歴
- `CHANGELOG.md` — バージョン履歴
