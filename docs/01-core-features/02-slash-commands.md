# 1-2: スラッシュコマンド一覧

> **学習時間**: 15分 | **難易度**: ⭐⭐

## スラッシュコマンドとは

スラッシュコマンドは `/` で始まるショートカットで、特定の機能やスキルを即座に呼び出せます。**組み込みコマンド**（固定のロジックを実行）と、**バンドルスキル**（Claude への指示ベースで動作）と、ユーザー定義の**カスタムスキル**の3種類があります。

`/` を入力すると補完リストが表示されます。コマンドはメッセージの先頭でのみ認識され、その後に続くテキストは引数として渡されます。

## 組み込みコマンド一覧

### セッション管理

| コマンド | 説明 |
|---------|------|
| `/help` | 利用可能なコマンドとヘルプを表示 |
| `/clear [name]` | 会話履歴をクリアして新しい会話を開始。以前の会話は `/resume` で復元可能。エイリアス: `/reset`, `/new` |
| `/compact [instructions]` | 会話を要約してコンテキストを節約。引数でサマリーの焦点を指定可能 |
| `/context [all]` | コンテキスト使用状況を色付きグリッドで可視化。最適化の提案も表示 |
| `/config [key=value]` | 設定を対話的に変更（`/settings` でも可）。`key=value` 形式で直接設定も可能 |
| `/status` | バージョン・モデル・アカウント・接続状態を表示（Claude 応答中でも使用可能） |
| `/fast [on\|off]` | Fast モードの切り替え |
| `/model [model]` | AI モデルを切り替えてデフォルトとして保存 |
| `/effort [level\|auto]` | 努力レベルを調整（low / medium / high / xhigh / max / ultracode） |
| `/plan [description]` | プランモードに入る。引数でタスクを指定可能 |
| `/exit` | Claude Code を終了。エイリアス: `/quit` |
| `/resume [session]` | 過去のセッションを再開（セッション名または ID で指定） |
| `/rename [name]` | セッションに名前を付ける |
| `/branch [name]` | 会話をブランチしてコピーを作成 |
| `/fork <directive>` | 現在の会話を引き継いだバックグラウンドサブエージェントを起動 |
| `/rewind` | チェックポイントに巻き戻す。エイリアス: `/checkpoint`, `/undo` |
| `/export [filename]` | 会話をテキストファイルに書き出す |
| `/goal [condition\|clear]` | ゴールを設定（条件が満たされるまで Claude が自律作業を継続） |
| `/btw <question>` | 会話履歴に残さずサイドクエスチョンを送る |
| `/recap` | 現在のセッションの要約を生成 |
| `/copy [N]` | 最後のアシスタント応答をクリップボードにコピー |

### ファイル・プロジェクト

| コマンド | 説明 |
|---------|------|
| `/init` | CLAUDE.md を自動生成（プロジェクト分析） |
| `/memory` | CLAUDE.md とオートメモリを管理 |
| `/diff` | インタラクティブな差分ビューアを開く |
| `/add-dir <path>` | セッションに作業ディレクトリを追加 |
| `/cd <path>` | セッションの作業ディレクトリを変更 |

### 並列処理・バックグラウンド

| コマンド | 説明 |
|---------|------|
| `/agents` | サブエージェントの設定を管理 |
| `/tasks` | バックグラウンドで実行中のタスクを表示。エイリアス: `/bashes` |
| `/background [prompt]` | セッションをバックグラウンドに移行してターミナルを解放。エイリアス: `/bg` |
| `/batch <instruction>` | **[スキル]** 大規模変更を並列サブエージェントで実行（各サブエージェントは独立した git worktree で動作） |
| `/schedule [description]` | ルーティン（定期実行タスク）を設定。エイリアス: `/routines` |
| `/loop [interval] [prompt]` | **[スキル]** プロンプトを定期的に繰り返し実行 |

### コードレビュー・品質

| コマンド | 説明 |
|---------|------|
| `/code-review [level] [--fix] [--comment] [target]` | **[スキル]** コードレビュー（`--fix` で自動修正、`ultra` でクラウドレビュー） |
| `/simplify [target]` | **[スキル]** コードの簡略化・リファクタリングを適用（バグ検出は行わない） |
| `/security-review` | セキュリティ脆弱性を分析 |
| `/review [PR]` | プルリクエストをローカルでレビュー |
| `/ultrareview [PR]` | クラウドでのディープレビュー（`/code-review ultra` の別名） |

### スキル・ツール

| コマンド | 説明 |
|---------|------|
| `/run-skill-generator` | `/run` や `/verify` 用のスキルを生成 |
| `/run` | **[スキル]** アプリを起動して動作確認 |
| `/verify` | **[スキル]** コード変更が実際に動作することを確認 |
| `/debug [description]` | **[スキル]** デバッグログを有効化して問題を診断 |
| `/skills` | 利用可能なスキルの一覧を表示 |

### 設定・診断

| コマンド | 説明 |
|---------|------|
| `/doctor` | インストールと設定を診断（`f` キーで問題を自動修正） |
| `/permissions` | ツール許可ルールを管理。エイリアス: `/allowed-tools` |
| `/hooks` | フック設定を確認 |
| `/mcp [reconnect\|enable\|disable]` | MCP サーバーの接続を管理 |
| `/ide` | IDE 連携の状態を確認・管理 |
| `/feedback [report]` | フィードバックやバグを報告。エイリアス: `/bug`, `/share` |
| `/keybindings` | キーボードショートカットファイルを開く |
| `/usage` | セッションコスト・プラン使用量・統計を表示。エイリアス: `/cost`, `/stats` |

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

## /fast モードについて

`/fast` コマンドは Fast モードのオン/オフを切り替えます。Fast モードは特定のプランや環境での高速応答モードです。

```
/fast on    # Fast モードを有効化
/fast off   # Fast モードを無効化
/fast       # 現在の状態をトグル
```

## /plan の使い方

`/plan` コマンドはプランモードに入り、コードを変更する前にアプローチを設計できます：

```
/plan 認証バグを修正する
```

Claude はコードに触れずに計画を立て、承認後に実装を開始します。

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

`key=value` 形式での直接設定も可能：
```
/config theme=dark
/config model=sonnet
/config thinking=false
```

## カスタムスラッシュコマンド

`.claude/skills/<name>/SKILL.md` を配置すると `/name` で呼び出せます：

```
.claude/skills/
└── deploy-check/
    └── SKILL.md   → /deploy-check として使える
```

`.claude/commands/deploy.md` のような旧形式のカスタムコマンドも引き続き動作します。スキルはカスタムコマンドと同名の場合、スキルが優先されます。

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

## 🏋️ 練習問題

1. **【確認】** `/clear` と `/reset` の違いを説明してください。
2. **【実践】** `/help` を実行して利用可能なコマンド一覧を確認しましょう。
3. **【実践】** `/code-review` を実行してみましょう。どのような出力が返りますか？
4. **【応用】** 長い会話セッションで `/compact` を使うのはどのような状況ですか？

## 次のステップ

→ [1-3: ターミナル・Bash ツール](03-terminal-bash.md) に進む
