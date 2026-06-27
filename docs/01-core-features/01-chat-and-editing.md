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

Claude Code は要件を解釈し、`Write` ツールを使って `Button.tsx` を新規作成します。

## ファイル編集

```
> src/utils/helpers.ts の formatDate 関数を
> ISO 8601 形式に対応するよう修正して
```

`Edit` ツールを使って既存ファイルを編集します。Edit ツールは**完全一致文字列置換**方式で動作します（正規表現や曖昧一致は使用しません）。ファイル全体を書き直すのではなく、変更箇所だけを差分更新します。

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

ターミナルから使う場合は、自然言語でファイルパスを指示するか、パイプでファイルの内容を渡します：

```bash
cat src/utils/helpers.ts | claude -p "この関数の問題点を教えて"
```

## 複数ファイルの同時編集

Claude Code は1回の指示で複数ファイルを同時に変更できます：

```
> Button コンポーネントの props に color プロパティを追加して。
> Button.tsx, Button.test.tsx, Storybook の Button.stories.tsx も更新して
```

## ツール一覧

Claude Code が内部で使う主要ツール：

| ツール | 説明 | 権限要否 |
|-------|------|---------|
| `Read` | ファイルの内容を読む | 不要 |
| `Edit` | ファイルの一部を完全一致置換で差分編集 | 要 |
| `Write` | 新しいファイルを作成、または既存ファイルを上書き | 要 |
| `Glob` | ファイルパターンでファイルを検索 | 不要 |
| `Grep` | ファイル内容を全文検索（ripgrep ベース） | 不要 |
| `Bash` | シェルコマンドを実行 | 要 |
| `PowerShell` | PowerShell コマンドを実行（Windows 環境） | 要 |
| `Agent` | サブエージェントを起動して独立したタスクを処理 | 不要 |
| `LSP` | 言語サーバーによるコード補完・参照解析 | 不要 |
| `WebFetch` | URL からコンテンツを取得 | 要 |
| `WebSearch` | Web 検索を実行 | 要 |
| `NotebookEdit` | Jupyter ノートブックのセルを編集 | 要 |
| `AskUserQuestion` | 要件確認のための多択質問 | 不要 |
| `Monitor` | バックグラウンドでコマンドを監視して変化に反応 | 要 |

> **注意**: ツールの利用可能性はプランや環境によって異なります。現在セッションで使えるツールを確認するには「利用可能なツールは何ですか？」と聞いてください。

## 承認フロー

破壊的な操作は実行前に確認を求めます：

```
Claude: 以下のファイルを削除しようとしています：
  - src/legacy/old-api.ts

実行しますか？ [y/N]
```

`settings.json` の `permissions.allow` に追加すると自動承認できます。

## 🏋️ 練習問題

1. **【確認】** Claude Code がファイルを編集する際に使う主要ツールを3つ答えてください。
2. **【実践】** 任意のプロジェクトで「このファイルのコードをリファクタリングして」と依頼してみましょう。
3. **【実践】** `> src/` のようにフォルダをコンテキストとして渡す方法を試してみましょう。
4. **【応用】** 大きなリファクタリングを依頼するとき、一度に全部頼むのとステップに分けて頼むのはどちらが良いですか？

## 次のステップ

→ [1-2: スラッシュコマンド一覧](02-slash-commands.md) に進む
