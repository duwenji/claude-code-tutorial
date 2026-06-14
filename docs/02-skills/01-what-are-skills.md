# 2-1: スキルとは

> **学習時間**: 15分 | **難易度**: ⭐⭐

## 概要

**スキル（Skills）** は、Claude Code に特定のタスクを実行させるための指示書です。`SKILL.md` というファイルに手順・ルール・出力形式を記述し、Claude Code が自動的に読み込んで実行します。

スキルは **Agent Skills オープンスタンダード**（[agentskills.io](https://agentskills.io)）に基づく共通フォーマットで、Claude Code と GitHub Copilot の両方で使えます。

## スキルの仕組み

```
ユーザー: 「このコードをレビューして」
    │
    ▼
Claude Code が description を検索
    │
    ▼
.claude/skills/code-review/SKILL.md を発見
    │
    ▼
SKILL.md の手順に従ってレビュー実行
    │
    ▼
構造化された出力を返す
```

## SKILL.md の構造

```markdown
---
name: code-review
description: コードの品質・セキュリティ・パフォーマンスをレビューします
tags:
  - review
  - quality
---

# コードレビュースキル

## 概要
指定されたコードを複数の観点でレビューし、改善点を提示します。

## 手順
1. コードを読み込む
2. 品質・セキュリティ・パフォーマンスの観点でチェック
3. 優先度付きで改善点を列挙

## 出力形式
...
```

## フロントマターの役割

| フィールド | 役割 |
|-----------|------|
| `name` | スキルの識別子。ディレクトリ名と一致させる |
| `description` | 自動読み込みの判断に使う説明文。具体的に書く |
| `tags` | カテゴリ分類（検索・発見に使用） |

`description` が特に重要です。Claude Code はこのフィールドを見て「今の作業にこのスキルが必要か」を判断します。

## スキルの配置場所

| 種類 | パス | 適用範囲 |
|------|------|---------|
| 個人用 | `~/.claude/skills/<name>/SKILL.md` | 全プロジェクト |
| プロジェクト用 | `.claude/skills/<name>/SKILL.md` | そのプロジェクトのみ |

## スキルが起動するタイミング

**1. 自動読み込み**  
会話の内容がスキルの `description` にマッチすると自動的に読み込まれます。

**2. スラッシュコマンドで明示呼び出し**  
```
/code-review
/debug
/my-custom-skill
```

## ディレクトリ構造

```
.claude/skills/
└── code-review/
    ├── SKILL.md           # メインの指示（必須）
    ├── scripts/           # 補助スクリプト（任意）
    │   └── check.sh
    ├── references/        # 参照ドキュメント（任意）
    │   └── style-guide.md
    └── assets/            # テンプレート等（任意）
        └── report.md
```

## 組み込みスキル vs カスタムスキル

| 種類 | 説明 | 例 | 適用場面 |
|------|------|-----|---------|
| 組み込みスキル | Claude Code に標準搭載 | `/code-review`, `/debug`, `/run` | 設定不要ですぐ使える |
| バンドルスキル | `~/.claude/skills/` に追加済み | `/skill-creator` | インストール後に全プロジェクトで使える |
| プロジェクトスキル | `.claude/skills/` に自分で追加 | `/deploy-check` | チームで共有したいワークフロー |
| 個人スキル | `~/.claude/skills/` に自分で追加 | `/my-workflow` | 自分だけのカスタム手順 |

### 組み込みスキル

Claude Code にあらかじめ搭載されているスキルです。インストール直後から `/code-review` や `/debug` などがすぐに使えます。設定・追加作業は一切不要です。

### バンドルスキル

Claude Code のインストール時に `~/.claude/skills/` に同梱されるスキルです。`/skill-creator` のように、スキルそのものを作るためのスキルが含まれます。全プロジェクトで共通して利用できます。

### プロジェクトスキル

リポジトリ内の `.claude/skills/` に配置するスキルです。デプロイ手順やチーム固有のレビュー基準など、**プロジェクト固有のワークフロー**を定義するのに適しています。git で管理することでチームメンバー全員が同じスキルを共有できます。

### 個人スキル

`~/.claude/skills/` に自分で追加するスキルです。よく使う個人的な作業手順やフォーマットなど、**自分だけのカスタムワークフロー**を定義します。すべてのプロジェクトに横断して適用されます。

## GitHub Copilot との互換性

同じ SKILL.md を `.github/skills/<name>/SKILL.md` に置くと GitHub Copilot でも使えます。Agent Skills オープンスタンダードに準拠しているためです。

## 次のステップ

→ [2-2: /skill-creator ハンズオン](02-skill-creator.md) に進む
