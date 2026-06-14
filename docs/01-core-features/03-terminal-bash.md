# 1-3: ターミナル・Bash ツール

> **学習時間**: 15分 | **難易度**: ⭐⭐

## Bash ツールとは

Claude Code は Bash ツールを通じてシェルコマンドを実行できます。これにより、ビルド・テスト・デプロイなどの実際の作業を自律的に行えます。

## 基本的な使い方

```
> npm のテストを実行して、失敗しているテストを教えて
```

Claude Code は内部で以下を実行します：
```bash
npm test
```

出力を解析して失敗しているテストを特定し、修正案を提示します。

## Git 操作

```
> 変更内容を確認してコミットメッセージを提案して
```

```bash
# Claude Code が実行する一連のコマンド
git status
git diff
git diff --staged
# → コミットメッセージを提案
```

```
> 現在のブランチの変更を main にマージして、コンフリクトがあれば解決して
```

## ビルドとテスト

```
> ビルドして、エラーがあれば修正して
```

```bash
npm run build
# エラーを解析 → 関連ファイルを修正 → 再ビルド
```

## パッケージ管理

```
> date-fns をインストールして、既存の日付処理を date-fns に置き換えて
```

```bash
npm install date-fns
# → 関連ファイルを自動編集
```

## 権限とセキュリティ

### 自動承認のルール

デフォルトでは危険な操作は確認が必要です。`settings.json` で細かく制御できます：

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm run *)",
      "Bash(npm test)",
      "Read(**)",
      "Edit(**)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

### パターン構文

| パターン | 意味 |
|---------|------|
| `Bash(git *)` | git で始まる全コマンドを許可 |
| `Bash(npm run *)` | npm run から始まる全コマンドを許可 |
| `Read(**)` | 全ファイルの読み込みを許可 |
| `Bash(rm -rf *)` | rm -rf を拒否 |

## バックグラウンド実行

長時間かかるコマンドはバックグラウンドで実行できます：

```
> 開発サーバーをバックグラウンドで起動して、起動完了を確認して
```

```bash
npm run dev &
# 起動ログを監視 → "Server running" を検出 → 確認完了
```

## 複数コマンドの連携

```
> 依存関係をインストールして、ビルドして、テストを実行して、全部成功したら git commit して
```

```bash
npm install && npm run build && npm test && git add -A && git commit -m "..."
```

Claude Code は各ステップの結果を確認し、失敗した場合は対処してから次に進みます。

## よく使うパターン

### プロジェクトの現状把握
```
> このプロジェクトのテストカバレッジを計測して
> npm run test:coverage
```

### リント・フォーマット
```
> ESLint と Prettier を実行して、自動修正できるものは直して
> npm run lint:fix && npm run format
```

### ポート確認
```
> 3000番ポートを使っているプロセスを教えて
```

## 次のステップ

→ [1-4: コンテキスト管理](04-context-management.md) に進む
