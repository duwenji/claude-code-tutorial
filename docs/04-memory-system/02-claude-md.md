# 4-2: CLAUDE.md ファイル

> **学習時間**: 15分 | **難易度**: ⭐⭐

## CLAUDE.md とは

`CLAUDE.md` はプロジェクトルートに置く指示書です。Claude Code はセッション開始時（および `/clear` 後）に自動的に読み込みます。

CLAUDE.md に書いたことは、毎回会話で説明しなくても常に有効です。

## 自動生成

```bash
claude
/init
```

Claude Code がプロジェクトを分析して CLAUDE.md の初稿を自動生成します。生成後、手動で調整します。

## 推奨構造

```markdown
# プロジェクト名

## 概要
このプロジェクトの1〜2行の説明。

## 技術スタック
- 言語: TypeScript 5.x
- フレームワーク: React 18
- テスト: Vitest + Testing Library
- スタイル: Tailwind CSS
- ビルド: Vite

## よく使うコマンド
- `npm run dev` — 開発サーバー起動（ポート 3000）
- `npm test` — テスト実行（watch モード）
- `npm run build` — プロダクションビルド
- `npm run lint` — ESLint チェック
- `npm run format` — Prettier フォーマット

## ディレクトリ構造
src/
├── components/   # UI コンポーネント
├── hooks/        # カスタムフック
├── utils/        # ユーティリティ関数
├── types/        # TypeScript 型定義
└── api/          # API クライアント

## コーディング規約
- コンポーネントは関数コンポーネントのみ（クラスコンポーネント禁止）
- Props の型は interface で定義（type alias 禁止）
- テストファイルはコンポーネントと同じディレクトリに配置
- インポートは絶対パス（`@/` エイリアス）を使用

## 注意事項
- src/legacy/ は触らない（リプレイス予定）
- 環境変数は .env.local に書く（.env は git 管理）
- API モックは msw を使用（fetch のモックは禁止）
```

## CLAUDE.md を書く場所

複数の CLAUDE.md を階層的に配置できます：

```
my-project/
├── CLAUDE.md          # プロジェクト全体の指示
├── src/
│   └── CLAUDE.md      # src/ 以下での作業時に追加読み込み
└── docs/
    └── CLAUDE.md      # docs/ 以下での作業時に追加読み込み
```

## 書くべき内容・書かない内容

| 書くべき | 書かない |
|---------|---------|
| 技術スタックとバージョン | コードの実装詳細（コードを読めばわかる） |
| よく使うコマンド | git 履歴（`git log` で確認できる） |
| コーディング規約 | 一時的なタスクの状態 |
| 触ってはいけないファイル | 汎用的な開発常識 |
| 外部ツールの接続情報 | 既に CLAUDE.md に書いてある内容の繰り返し |

## 効果的な書き方のコツ

### 理由を添える

```markdown
# 悪い例
- テストには Vitest を使う

# 良い例
- テストには Vitest を使う（Jest から移行済み。新しく Jest を追加しないこと）
```

### 禁止事項は明確に

```markdown
## 禁止事項
- `any` 型の使用（TypeScript の型安全性を損なう）
- `console.log` をコミットすること（デバッグ用のみ）
- `src/legacy/` の直接編集（移行チームの担当）
```

### バージョン依存を明示する

```markdown
## バージョン
- React 18.3.x（新しい Hooks は 18.3+ 対応のものを使う）
- Node.js 20.x（ESM モジュールを使用）
```

## 次のステップ

→ [4-3: 永続的なメモリ](03-persistent-memory.md) に進む
