# 4-3: 永続的なメモリ

> **学習時間**: 15分 | **難易度**: ⭐⭐

## セッションをまたいだ記憶

Claude Code のメモリシステムは `~/.claude/projects/<hash>/memory/` にファイルとして保存されます。新しいセッションを開始したときや `/clear` 後でも、このメモリは引き継がれます。

## メモリの読み込みタイミング

```
セッション開始
    │
    ▼
CLAUDE.md を読み込む
    │
    ▼
MEMORY.md（インデックス）を読み込む
    │
    ▼
関連する個別メモリを読み込む
    │
    ▼
会話開始
```

## メモリの保存・更新

### 自動保存

Claude Code が重要な情報を検出すると自動的にメモリを作成します：

```
> テストフレームワークは Vitest に統一することになりました

Claude: 了解しました。メモリに保存しておきます。
[memory/feedback-testing.md を作成]
```

### 手動で保存を依頼

```
> 「PR は必ず 1 機能ずつ分けてレビューしてもらう」という方針を覚えておいて

Claude: 保存しました。
[memory/project-pr-policy.md を作成]
```

## MEMORY.md インデックス

各メモリへのポインタとなるインデックスファイルです。セッション開始時に必ず読み込まれるため、200行以内に収めるのが目安です：

```markdown
# Memory Index

## ユーザー
- [user-role](user-role.md) — フルスタック開発者、React/TypeScript + Go 専門、Docker○ Kubernetes学習中

## フィードバック
- [feedback-no-trailing-summary](feedback-no-trailing-summary.md) — レスポンス末尾のサマリー不要（diff を直接確認するため）
- [feedback-testing](feedback-testing.md) — テストフレームワークは Vitest のみ使用、Jest 禁止

## プロジェクト
- [project-auth](project-auth.md) — 認証モジュール JWT→Session 移行中（期限: 2026-06-30）
- [project-freeze](project-freeze.md) — 2026-06-20 以降マージ凍結（モバイルリリース対応）

## 参照
- [reference-design](reference-design.md) — Figma "Product v3" ファイル、Linear "Frontend" プロジェクト
```

### 対応するメモリファイルのサンプル

MEMORY.md の各行が指すファイルはこのような内容になります：

**`feedback-testing.md`**

```markdown
---
name: feedback-testing
description: テストフレームワークは Vitest のみ（Jest 使用禁止）
metadata:
  type: feedback
---

テストは必ず Vitest を使う。Jest は使わない。

**Why:** 昨年 Jest のモックが本番と挙動が異なり、リリース後に障害が発生した。
**How to apply:** テストコードを書くとき・修正するとき全般に適用。
```

**`project-auth.md`**

```markdown
---
name: project-auth
description: 認証モジュール書き換えプロジェクト（期限 2026-06-30）
metadata:
  type: project
---

認証モジュールを JWT から Session-based に移行中。期限: 2026-06-30。

**Why:** セキュリティ監査でセッション管理の不備が指摘された（コンプライアンス要件）。
**How to apply:** 認証関連の変更はセッション方式を前提に提案する。エルゴノミクスより準拠を優先。
```

## メモリの確認

```
> 今どんなことを覚えているか教えて
```

```
> feedback に関するメモリを全部見せて
```

## メモリの更新

```
> auth プロジェクトの期限が 7 月末に変更になったので更新して
```

## メモリの削除

```
> project-auth のメモリは完了したので削除して
```

## メモリとして保存すべき情報

| 保存する | 保存しない |
|---------|---------|
| ユーザーの技術的背景 | 実装の詳細（コードを見ればわかる） |
| 繰り返し出てきた要望 | 一時的なデバッグの結果 |
| プロジェクトの方針・制約 | 汎用的な技術情報 |
| 外部ツールのリンク | 現在の会話コンテキスト |
| 確認済みのアプローチ | git 履歴で追える情報 |

## メモリファイルの手動編集

メモリファイルはただの Markdown ファイルなので、直接編集できます：

```bash
code ~/.claude/projects/<hash>/memory/feedback-testing.md
```

## メモリの有効期限に注意

メモリは「書いたときに正しかった情報」です。コードやプロジェクトが変わっても、メモリは自動更新されません。定期的に見直して古くなったメモリを削除・更新しましょう。

```
> メモリの内容を確認して、現在の状況と合っているか確認して
```

## 次のステップ

→ [4-4: メモリのベストプラクティス](04-memory-best-practices.md) に進む
