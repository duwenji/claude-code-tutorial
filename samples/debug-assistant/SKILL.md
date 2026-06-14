---
id: debug-assistant
name: デバッグアシスタント
description: >
  エラーメッセージ・スタックトレース・予期しない動作を分析し、根本原因を特定して修正します。
  "エラー", "バグ", "動かない", "クラッシュ", "error", "bug", "fix" という言葉が出たときに自動的に提案します。
tags:
  - debug
  - error-analysis
  - troubleshooting
---

# デバッグアシスタントスキル

## 概要

エラーメッセージ・スタックトレース・予期しない動作を受け取り、根本原因を特定して修正案を提示します。必要に応じてコードを直接修正します。

## 利用シーン

- ビルドエラー・実行時エラーの解決
- テストが失敗している原因の特定
- 予期しない動作（バグ）の調査

## 入力パラメータ

| パラメータ | 型 | 必須 | 説明 |
|-----------|------|------|------|
| `error` | string | ✅ | エラーメッセージまたはスタックトレース |
| `context` | string | ❌ | 追加のコンテキスト（再現手順など） |
| `auto_fix` | boolean | ❌ | 自動修正するか（デフォルト: true） |

## デバッグ手順

### Step 1: エラーの分類

エラーの種類を特定する：
- **構文エラー**: SyntaxError, ParseError
- **型エラー**: TypeError, TypeScript エラー
- **実行時エラー**: ReferenceError, undefined
- **非同期エラー**: Promise rejection, async/await
- **外部エラー**: API エラー, DB エラー, ネットワークエラー

### Step 2: 関連ファイルの特定

スタックトレースから関連ファイルを特定し、読み込む：
1. スタックトレースのファイルパスを抽出
2. エラーが発生した行を中心に前後30行を読む
3. インポートしているファイルも必要に応じて確認する

### Step 3: 根本原因の分析

以下の観点で原因を分析する：
- エラーが発生した直接の原因
- その原因を引き起こした間接的な原因
- 同様のパターンが他の場所にないか

### Step 4: 修正と確認

1. 修正案を提示する
2. `auto_fix: true` の場合は実際にファイルを修正する
3. 修正後にビルドまたはテストを実行して確認する

## 出力形式

### エラー分析レポート

**エラータイプ**: TypeError / ReferenceError / etc.

**根本原因**:
[原因の説明]

**発生箇所**: `src/components/UserList.tsx:23`

**修正方法**:
```typescript
// Before
const name = user.profile.name;

// After
const name = user.profile?.name ?? '名前なし';
```

**類似パターン**: [他に同じ問題がある場合は一覧表示]

## テストケース

| # | エラー | 期待結果 |
|---|-------|---------|
| 1 | `TypeError: Cannot read properties of undefined` | 原因特定 + optional chaining で修正 |
| 2 | TypeScript コンパイルエラー | 型エラーの修正 |
| 3 | React `useEffect` の無限ループ | 依存配列の問題を特定して修正 |
