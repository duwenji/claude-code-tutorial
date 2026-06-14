# 1-1: チャットとファイル編集

> **学習時間**: 15分 | **難易度**: ⭐⭐

## 基本的な会話

Claude Code は対話形式で作業します。自然言語で指示を出すだけで、必要なツールを自律的に選択して実行します。

```
> このディレクトリにある TypeScript ファイルの一覧を教えて

Claude: 確認します。
[Glob ツールを実行: **/*.ts]

src/
├── index.ts
├── components/Button.tsx
└── utils/helpers.ts
```

## ファイル読み込み

Claude Code はプロジェクト内のファイルを直接読み込んで作業します。

```
> src/utils/helpers.ts を読んで、何をしているか説明して
```

明示的にファイルを指定しなくても、文脈から関連ファイルを自動的に読み込みます。

## コード生成

```
> React コンポーネント Button.tsx を作成して。
> プロパティ: label(string), onClick(function), disabled(boolean)
> スタイル: Tailwind CSS を使用
```

Claude Code は要件を解釈し、`Button.tsx` を作成します。

## ファイル編集

```
> src/utils/helpers.ts の formatDate 関数を
> ISO 8601 形式に対応するよう修正して
```

Edit ツールを使って既存ファイルを差分で編集します（ファイル全体の書き直しはしません）。

## リファクタリング

```
> UserList コンポーネントのパフォーマンスを改善して。
> 不要な再レンダリングを防ぐよう useMemo と useCallback を適切に使って
```

## デバッグ補助

エラーメッセージをそのまま貼り付けるだけで原因を特定します：

```
> npm run build で以下のエラーが出ます：
> TypeError: Cannot read properties of undefined (reading 'map')
>   at UserList (src/components/UserList.tsx:23:18)
```

## ファイル・フォルダの参照

`@` 記法でファイルやフォルダを会話に含められます（IDE 拡張のみ）：

```
@src/components/Button.tsx をレビューして
@src/utils/ の全ファイルの依存関係を整理して
```

## 複数ファイルの同時編集

Claude Code は1回の指示で複数ファイルを同時に変更できます：

```
> Button コンポーネントの props に color プロパティを追加して。
> Button.tsx, Button.test.tsx, Storybook の Button.stories.tsx も更新して
```

## ツール一覧

Claude Code が内部で使うツール：

| ツール | 用途 |
|-------|------|
| `Read` | ファイルの内容を読む |
| `Edit` | ファイルの一部を差分編集 |
| `Write` | 新しいファイルを作成 |
| `Glob` | ファイルパターン検索 |
| `Grep` | ファイル内容の全文検索 |
| `Bash` | シェルコマンドを実行 |
| `Agent` | サブエージェントを起動 |

## 承認フロー

破壊的な操作は実行前に確認を求めます：

```
Claude: 以下のファイルを削除しようとしています：
  - src/legacy/old-api.ts

実行しますか？ [y/N]
```

`settings.json` の `permissions.allow` に追加すると自動承認できます。

## 次のステップ

→ [1-2: スラッシュコマンド一覧](02-slash-commands.md) に進む
