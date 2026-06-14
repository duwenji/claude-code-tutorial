# 4-1: メモリの種類

> **学習時間**: 15分 | **難易度**: ⭐⭐

## メモリシステムとは

Claude Code のメモリシステムは、セッションをまたいで情報を保持するための仕組みです。会話が終わっても、重要な情報を次のセッションで利用できます。

## 4種類のメモリ

### user（ユーザー情報）

ユーザーの役割・目標・技術レベル・好みを記録します。

```markdown
---
name: user-role
description: ユーザーの役割と技術スタック
metadata:
  type: user
---

フルスタック開発者。フロントエンドは React/TypeScript が得意、
バックエンドは Go を使用。Docker は使いこなしているが、
Kubernetes は学習中。
```

**用途**: 説明の詳しさ、使う言語、前提とする知識レベルの調整。

---

### feedback（フィードバック）

Claude への指示・修正・確認済みのアプローチを記録します。

```markdown
---
name: feedback-no-trailing-summary
description: レスポンス末尾のサマリー不要
metadata:
  type: feedback
---

レスポンス末尾に「〜を行いました」というサマリーを書かない。
ユーザーは diff を直接確認できるので不要。

**Why:** ユーザーが「末尾のサマリーは不要」と明示的に指示。
**How to apply:** 全レスポンスで適用。コード変更後のまとめも省略。
```

**用途**: 同じ修正を繰り返し求める手間を省く。

---

### project（プロジェクト情報）

進行中の作業・目標・決定事項・期限を記録します。

```markdown
---
name: project-auth-rewrite
description: 認証モジュール書き換えプロジェクト
metadata:
  type: project
---

認証モジュールを JWT から Session-based に移行中。
期限: 2026-06-30。

**Why:** セキュリティ監査でセッション管理の不備が指摘された。
**How to apply:** 認証関連の変更提案はセッション方式を前提にする。
```

**用途**: 長期プロジェクトの文脈を毎回説明せずに済む。

---

### reference（参照情報）

外部システムのリンク・リポジトリ・ドキュメントの場所を記録します。

```markdown
---
name: reference-bug-tracker
description: バグ追跡システムの場所
metadata:
  type: reference
---

バグは Linear の "Frontend" プロジェクトで管理。
デザインは Figma の "Product v3" ファイル。
```

**用途**: 外部ツールを毎回説明せずに参照できる。

## メモリの保存場所

```
~/.claude/projects/<project-hash>/memory/
├── MEMORY.md           # インデックス（全メモリのリスト）
├── user-role.md
├── feedback-no-summary.md
├── project-auth.md
└── reference-tools.md
```

`MEMORY.md` はインデックスとして機能し、各セッション開始時に読み込まれます。

## メモリの使い方

### 保存する

```
> この設定を覚えておいて：テストは必ず Vitest を使うこと
```

Claude Code は自動的にメモリファイルを作成します。

### 確認する

```
> 今まで何を覚えているか教えて
```

### 削除する

```
> auth-rewrite プロジェクトのメモリを削除して
```

## 次のステップ

→ [4-2: CLAUDE.md ファイル](02-claude-md.md) に進む
