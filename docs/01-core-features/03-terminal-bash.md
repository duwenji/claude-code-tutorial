# 1-3: ターミナル・Bash ツール

> **学習時間**: 15分 | **難易度**: ⭐⭐

## Bash ツールとは

Claude Code は `Bash` ツールを通じてシェルコマンドを実行できます。これにより、ビルド・テスト・デプロイなどの実際の作業を自律的に行えます。

## Windows での PowerShell ツール

Windows 環境では、`PowerShell` ツールが追加で利用できます。

| 環境 | 動作 |
|------|------|
| Git Bash なし (Windows) | PowerShell ツールが自動で有効化 |
| Git Bash あり (Windows) | 段階的ロールアウト中 |
| Linux / macOS / WSL | `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` で有効化可能 |

`settings.json` で明示的に有効化することもできます：

```json
{
  "env": {
    "CLAUDE_CODE_USE_POWERSHELL_TOOL": "1"
  }
}
```

PowerShell ツールが有効な場合、Claude は PowerShell をプライマリシェルとして扱います。Git Bash がインストールされていれば、POSIX スクリプト用に Bash ツールも引き続き使用できます。

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

デフォルトでは危険な操作は確認が必要です。`settings.json` の `permissions` セクションで細かく制御できます：

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
| `Edit(/src/**)` | src/ 以下のファイル編集を許可 |
| `Bash(rm -rf *)` | rm -rf を拒否 |
| `WebFetch(domain:example.com)` | 特定ドメインへの WebFetch を許可 |

`Edit(...)` の許可ルールは同じパスへの Read アクセスも自動的に許可します。

### CLI フラグでの設定

セッション単位での制御は CLI フラグでも可能です：

```bash
# 特定コマンドを確認なしで実行
claude --allowedTools "Bash(git log *)" "Bash(git diff *)"

# 特定ツールを拒否
claude --disallowedTools "Bash(rm *)" "Edit"
```

### 危険なコマンドを防ぐ方法

`deny` ルールに追加することで、特定の危険なコマンドを常に拒否できます：

```json
{
  "permissions": {
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(DROP TABLE *)"
    ]
  }
}
```

また、`--permission-mode` フラグで動作モードを制御できます：
- `default` — デフォルト（要確認）
- `acceptEdits` — ファイル編集を自動承認
- `plan` — 計画のみ（実行しない）
- `bypassPermissions` — すべて自動承認（危険・開発環境のみ推奨）

## バックグラウンド実行

長時間かかるコマンドはバックグラウンドで実行できます：

```
> 開発サーバーをバックグラウンドで起動して、起動完了を確認して
```

```bash
npm run dev &
# 起動ログを監視 → "Server running" を検出 → 確認完了
```

バックグラウンドタスクの管理は `/tasks` コマンドで行います。

## 複数コマンドの連携

```
> 依存関係をインストールして、ビルドして、テストを実行して、全部成功したら git commit して
```

```bash
npm install && npm run build && npm test && git add -A && git commit -m "..."
```

Claude Code は各ステップの結果を確認し、失敗した場合は対処してから次に進みます。

## Bash ツールの制限

各コマンドには以下の制限があります：

- **タイムアウト**: デフォルト2分（最大10分まで拡張可能）
- **出力長**: デフォルト 30,000 文字（環境変数 `BASH_MAX_OUTPUT_LENGTH` で調整可能）

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

## 🏋️ 練習問題

1. **【確認】** Bash ツールと PowerShell ツールの違いを説明してください（Windows 環境の場合）。
2. **【実践】** `git status` を Claude Code 経由で実行してみましょう。
3. **【実践】** `settings.json` に `allowedTools` を設定して、特定コマンドを確認なしで実行できるようにしてみましょう。
4. **【応用】** 危険なコマンド（`rm -rf` 等）の実行を防ぐにはどうすれば良いですか？

## 次のステップ

→ [1-4: コンテキスト管理](04-context-management.md) に進む
