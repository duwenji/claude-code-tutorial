# 1-2: スラッシュコマンド一覧

> **学習時間**: 15分 | **難易度**: ⭐⭐

## スラッシュコマンドとは

スラッシュコマンドは `/` で始まるショートカットで、特定の機能やスキルを即座に呼び出せます。組み込みコマンドと、スキルとして定義されたカスタムコマンドの2種類があります。

## 組み込みコマンド一覧

### セッション管理

| コマンド | 説明 |
|---------|------|
| `/help` | 利用可能なコマンドとヘルプを表示 |
| `/clear` | 会話履歴をクリアして新しい会話を開始 |
| `/compact [instructions]` | 会話を要約してコンテキストを節約 |
| `/context` | コンテキスト使用状況を可視化 |
| `/config` | 設定を対話的に変更（`/settings` でも可） |
| `/status` | バージョン・モデル・アカウント・接続状態を表示 |
| `/fast [on\|off]` | Fast モード（Claude Opus 高速版）の切り替え |
| `/model [model]` | AI モデルを切り替え |
| `/effort [level]` | 努力レベルを調整（low / medium / high / xhigh / max） |
| `/exit` | Claude Code を終了 |
| `/resume [session]` | 過去のセッションを再開 |
| `/rename [name]` | セッションに名前を付ける |
| `/branch [name]` | 会話をブランチしてコピーを作成 |
| `/rewind` | チェックポイントに巻き戻す |
| `/export [filename]` | 会話をテキストファイルに書き出す |

### ファイル・プロジェクト

| コマンド | 説明 |
|---------|------|
| `/init` | CLAUDE.md を自動生成（プロジェクト分析） |
| `/memory` | CLAUDE.md とオートメモリを管理 |
| `/diff` | インタラクティブな差分ビューアを開く |
| `/add-dir <path>` | セッションに作業ディレクトリを追加 |

### 並列処理・バックグラウンド

| コマンド | 説明 |
|---------|------|
| `/agents` | サブエージェントの設定を管理 |
| `/tasks` | バックグラウンドで実行中のタスクを表示 |
| `/background [prompt]` | セッションをバックグラウンドに移行してターミナルを解放 |
| `/batch <instruction>` | 大規模変更を並列サブエージェントで実行 |
| `/schedule [description]` | ルーティン（定期実行タスク）を設定 |

### コードレビュー・品質

| コマンド | 説明 |
|---------|------|
| `/code-review [--fix] [--comment]` | コードレビュー（`--fix` で自動修正） |
| `/simplify [target]` | コードの簡略化・リファクタリングを適用 |
| `/security-review` | セキュリティ脆弱性を分析 |
| `/review [PR]` | プルリクエストをローカルでレビュー |
| `/ultrareview [PR]` | クラウドでのディープレビュー（`/code-review ultra` でも可） |

### スキル・ツール

| コマンド | 説明 |
|---------|------|
| `/run-skill-generator` | `/run` や `/verify` 用のスキルを生成 |
| `/run` | アプリを起動して動作確認 |
| `/verify` | コード変更が実際に動作することを確認 |
| `/debug [description]` | デバッグログを有効化して問題を診断 |
| `/skills` | 利用可能なスキルの一覧を表示 |

### 設定・診断

| コマンド | 説明 |
|---------|------|
| `/doctor` | インストールと設定を診断 |
| `/permissions` | ツール許可ルールを管理 |
| `/hooks` | フック設定を確認 |
| `/mcp` | MCP サーバーの接続を管理 |
| `/plan [description]` | プランモードに入る |
| `/ide` | IDE 連携の状態を確認・管理 |
| `/feedback [report]` | フィードバックやバグを報告 |

## コマンドの使い方

### 引数なし
```
/code-review
```
カレントブランチの変更差分をレビューします。

### 引数あり
```
/code-review --fix
/code-review src/components/Button.tsx
```

### スキルとして呼び出す
独自スキルも同じようにスラッシュで呼び出せます：
```
/my-custom-skill
```

## /init の使い方

プロジェクト初回セットアップ時に使います：

```bash
claude
/init
```

Claude Code がプロジェクトを分析して `CLAUDE.md` を生成します。生成される内容：
- プロジェクト概要
- 技術スタック
- よく使うコマンド
- コーディング規約の推測

## /config の使い方

```
/config
```

対話的に設定できる項目：
- モデルの選択
- テーマ（light / dark）
- 自動コンパクト設定
- 通知設定

## カスタムスラッシュコマンド

`.claude/skills/<name>/SKILL.md` を配置すると `/name` で呼び出せます：

```
.claude/skills/
└── deploy-check/
    └── SKILL.md   → /deploy-check として使える
```

## /help の出力例

実際のコマンド一覧はバージョンや環境によって異なります。`/` を入力すると補完リストが表示されます。

```
/help          Show help and available commands
/clear         Start a new conversation
/compact       Free up context by summarizing
/config        Open the Settings interface
/init          Initialize project with a CLAUDE.md guide
/model         Switch the AI model
/effort        Set the model effort level
/code-review   Review the current diff
/run           Launch and drive your project's app
/verify        Confirm a code change works
/debug         Enable debug logging
/doctor        Diagnose your Claude Code installation
/memory        Edit CLAUDE.md memory files
/agents        Manage agent configurations
...

Custom skills:
  /deploy-check  (from .claude/skills/deploy-check/)
```

## 次のステップ

→ [1-3: ターミナル・Bash ツール](03-terminal-bash.md) に進む
