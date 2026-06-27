# claude-code-tutorial 教材改善 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Claude Code チュートリアル全27セクションを、公式ドキュメントと照合して正確化し、各セクションに練習問題（3〜5問）を追加する。

**Architecture:** Part 0 から Part 6 まで順番にスプリントを実施する。各スプリントで公式ドキュメントを取得・比較し、セクションを更新して練習問題を追加してコミットする。

**Tech Stack:** Markdown、WebFetch（公式ドキュメント取得）、Git

## Global Constraints

- 本文はすべて日本語で記述する
- 練習問題は各セクション末尾の「次のステップ」リンクの直前に `## 🏋️ 練習問題` セクションとして追加する
- 練習問題は 3〜5 問（【確認】1〜2問 + 【実践】1〜2問 + 【応用】0〜1問）
- 正確性の基準は docs.anthropic.com の公式ドキュメント
- 未収録で独立セクションが必要な機能は ROADMAP.md に追記する
- 作業ベースディレクトリ: `c:\Dev\tutorials\claude-code-tutorial`

---

## Task 1: Part 0 — 基礎と環境準備

**Files:**
- Modify: `docs/00-fundamentals/01-what-is-claude-code.md`
- Modify: `docs/00-fundamentals/02-installation-setup.md`

**Interfaces:**
- Produces: 正確化・演習追加済みの Part 0 セクション（Part 1 以降の基準となるフォーマットの先例）

- [ ] **Step 1: 公式ドキュメント取得（概要・インストール）**

以下の URL から公式ドキュメントを取得する:
```
https://docs.anthropic.com/ja/docs/claude-code/overview
https://docs.anthropic.com/ja/docs/claude-code/setup
```
英語版にフォールバック:
```
https://docs.anthropic.com/en/docs/claude-code/overview
https://docs.anthropic.com/en/docs/claude-code/setup
```

- [ ] **Step 2: 01-what-is-claude-code.md を更新**

確認・修正ポイント:
- アーキテクチャ図のツール名が現在の実装と一致しているか（Read / Edit / Write / Bash / Glob / Grep）
- 他ツールとの比較表の内容が最新か（GitHub Copilot / Cursor の機能比較）
- 利用できる環境の一覧（iOS アプリ等）が現在も正確か
- 「Claude Code が得意なこと」の表に漏れがないか

- [ ] **Step 3: 01-what-is-claude-code.md に練習問題を追加**

「次のステップ」の直前に以下を追加する:

```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code が「単なるチャットボット」と異なる点を2つ挙げてください。
2. **【確認】** Claude Code の設計原則3つを説明してください。
3. **【実践】** Claude Code と GitHub Copilot の違いを、自分のユースケースに当てはめて考えてみましょう。どの機能が最も役立ちそうですか？
4. **【応用】** あなたが日常のコーディング作業でよく行うタスク（デバッグ、コードレビューなど）を1つ選び、Claude Code がどう支援できるか想像してみましょう。
```

- [ ] **Step 4: 02-installation-setup.md を更新**

確認・修正ポイント:
- インストールコマンドが現在も有効か（特にネイティブインストーラーの URL）
- `npm install -g @anthropic-ai/claude-code` の非推奨注意書きが適切か
- `claude doctor` コマンドの説明が正確か
- 認証方法（claude.ai プラン一覧・価格）が最新か
- モデル ID（`claude-opus-4-8` 等）が現在のラインナップと一致しているか
- 設定ファイルのパス一覧に漏れがないか

- [ ] **Step 5: 02-installation-setup.md に練習問題を追加**

「次のステップ」の直前に以下を追加する:

```markdown
## 🏋️ 練習問題

1. **【確認】** ネイティブインストーラーと npm インストールの違いを1つ答えてください。
2. **【実践】** `claude --version` と `claude doctor` を実行し、バージョンと診断結果を確認しましょう。
3. **【実践】** プロジェクトディレクトリで `claude` を起動し、`/init` で CLAUDE.md を生成してみましょう。
4. **【応用】** チームで Claude Code を導入する場合、API キー認証と claude.ai プラン認証のどちらが適切ですか？理由とともに答えてください。
```

- [ ] **Step 6: コミット**

```bash
git add docs/00-fundamentals/
git commit -m "docs(part0): 正確性更新・練習問題追加"
```

---

## Task 2: Part 1 — コア機能

**Files:**
- Modify: `docs/01-core-features/01-chat-and-editing.md`
- Modify: `docs/01-core-features/02-slash-commands.md`
- Modify: `docs/01-core-features/03-terminal-bash.md`
- Modify: `docs/01-core-features/04-context-management.md`

**Interfaces:**
- Consumes: Task 1 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの Part 1 セクション

- [ ] **Step 1: 公式ドキュメント取得（CLI・コマンド・ツール）**

```
https://docs.anthropic.com/en/docs/claude-code/cli-usage
https://docs.anthropic.com/en/docs/claude-code/slash-commands
https://docs.anthropic.com/en/docs/claude-code/tools
```

- [ ] **Step 2: 01-chat-and-editing.md を更新**

確認・修正ポイント:
- ファイル編集ツール（Edit / Write / Read）の説明が現在の仕様と一致しているか
- マルチファイル編集の手順が正確か
- コード生成・リファクタリングのサンプルプロンプトが実用的か

練習問題を「次のステップ」の直前に追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code がファイルを編集する際に使う主要ツールを3つ答えてください。
2. **【実践】** 任意のプロジェクトで「このファイルのコードをリファクタリングして」と依頼してみましょう。
3. **【実践】** `> src/` のようにフォルダをコンテキストとして渡す方法を試してみましょう。
4. **【応用】** 大きなリファクタリングを依頼するとき、一度に全部頼むのとステップに分けて頼むのはどちらが良いですか？
```

- [ ] **Step 3: 02-slash-commands.md を更新**

確認・修正ポイント:
- スラッシュコマンドの一覧が最新か（`/plan`, `/worktree` 等の新コマンドが抜けていないか）
- 各コマンドの説明・使い方が正確か
- `/fast` モードの説明が含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** `/clear` と `/reset` の違いを説明してください。
2. **【実践】** `/help` を実行して利用可能なコマンド一覧を確認しましょう。
3. **【実践】** `/code-review` を実行してみましょう。どのような出力が返りますか？
4. **【応用】** 長い会話セッションで `/compact` を使うのはどのような状況ですか？
```

- [ ] **Step 4: 03-terminal-bash.md を更新**

確認・修正ポイント:
- Windows での PowerShell / Bash ツールの使い分けが正確か
- 権限管理（`allowedTools` / `blockedTools`）の説明が最新の settings.json スキーマと一致しているか
- パイプラインやスクリプト実行の説明が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** Bash ツールと PowerShell ツールの違いを説明してください（Windows 環境の場合）。
2. **【実践】** `git status` を Claude Code 経由で実行してみましょう。
3. **【実践】** `settings.json` に `allowedTools` を設定して、特定コマンドを確認なしで実行できるようにしてみましょう。
4. **【応用】** 危険なコマンド（`rm -rf` 等）の実行を防ぐにはどうすれば良いですか？
```

- [ ] **Step 5: 04-context-management.md を更新**

確認・修正ポイント:
- コンテキスト圧縮（`/compact`）の仕組みが正確か
- ファイル・フォルダの渡し方の説明が最新か
- コンテキストウィンドウの制限に関する説明が現在の仕様と合っているか
- Plan モードについての言及があるか（なければ追記またはROADMAPへ）

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code がコンテキストを圧縮するのはどのような状況ですか？
2. **【実践】** 複数のファイルを一度にコンテキストに渡す方法を試してみましょう。
3. **【実践】** `/compact` を実行して、会話の圧縮がどのように行われるか観察しましょう。
4. **【応用】** 大きなリポジトリで作業する場合、コンテキストを効果的に管理するための戦略を考えてみましょう。
```

- [ ] **Step 6: コミット**

```bash
git add docs/01-core-features/
git commit -m "docs(part1): 正確性更新・練習問題追加"
```

---

## Task 3: Part 2 — スキルシステム

**Files:**
- Modify: `docs/02-skills/01-what-are-skills.md`
- Modify: `docs/02-skills/02-skill-creator.md`
- Modify: `docs/02-skills/03-built-in-skills.md`
- Modify: `docs/02-skills/04-custom-skills.md`

**Interfaces:**
- Consumes: Task 1-2 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの Part 2 セクション

- [ ] **Step 1: 公式ドキュメント取得（スキル）**

```
https://docs.anthropic.com/en/docs/claude-code/skills
https://docs.anthropic.com/en/docs/claude-code/slash-commands
```

- [ ] **Step 2: 01-what-are-skills.md を更新**

確認・修正ポイント:
- SKILL.md のフロントマターフィールド（`name` / `description` / `tags` 等）が現在の仕様と一致しているか（`id` フィールドの有無）
- スキルの配置パス（`~/.claude/skills/` vs `.claude/skills/`）が正確か
- GitHub Copilot との互換性の説明が現在も有効か
- 自動読み込みのトリガー条件が正確か

練習問題を追加（「次のステップ」直前）:
```markdown
## 🏋️ 練習問題

1. **【確認】** SKILL.md のフロントマターで必須のフィールドを全て答えてください。
2. **【確認】** プロジェクトスキルと個人スキルの保存場所の違いを説明してください。
3. **【実践】** `~/.claude/skills/` ディレクトリを確認し、インストール済みのスキルを一覧表示しましょう。
4. **【応用】** チームで統一したコーディング規約チェックを自動化するには、スキルをどこに配置するのが適切ですか？
```

- [ ] **Step 3: 02-skill-creator.md を更新**

確認・修正ポイント:
- `/skill-creator` コマンドが現在も利用可能か
- スキル生成のダイアログ・手順が現在の実装と一致しているか
- テスト・評価・改善サイクルの説明が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** `/skill-creator` を使ってスキルを作成する際の主なステップを3つ答えてください。
2. **【実践】** `/skill-creator` を起動して、簡単なスキル（例：コミットメッセージ生成）を作成してみましょう。
3. **【実践】** 作成したスキルをスラッシュコマンドで呼び出してみましょう。
4. **【応用】** 自動生成されたスキルをどのような観点で改善しますか？
```

- [ ] **Step 4: 03-built-in-skills.md を更新**

確認・修正ポイント:
- 組み込みスキル一覧（`/code-review`, `/debug`, `/run`, `/security-review` 等）が最新か
- 各スキルの動作・オプションが現在の仕様と一致しているか
- `/ultrareview` / `ultra` オプションの説明が含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** 組み込みスキルとカスタムスキルの違いを説明してください。
2. **【実践】** `/code-review` を実行して、出力の構造を確認しましょう。
3. **【実践】** `/security-review` を任意のファイルに対して実行してみましょう。
4. **【応用】** `/code-review` と `/security-review` を使い分ける基準を考えてみましょう。
```

- [ ] **Step 5: 04-custom-skills.md を更新**

確認・修正ポイント:
- 手書き SKILL.md の best practices が現在の推奨事項と一致しているか
- フロントマターの記述例が正確なフォーマットになっているか
- スキルのテスト方法の説明が含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** SKILL.md の `description` フィールドが重要な理由を説明してください。
2. **【実践】** 自分のよく使う作業（例：PR 説明文の生成）をカスタムスキルとして作成してみましょう。
3. **【実践】** 作成したスキルを `.claude/skills/` に配置し、スラッシュコマンドで呼び出しましょう。
4. **【応用】** スキルの `description` にトリガーキーワードを含めることで、どのようなメリットがありますか？
```

- [ ] **Step 6: コミット**

```bash
git add docs/02-skills/
git commit -m "docs(part2): 正確性更新・練習問題追加"
```

---

## Task 4: Part 3 — フックと自動化

**Files:**
- Modify: `docs/03-hooks-automation/01-hooks-overview.md`
- Modify: `docs/03-hooks-automation/02-hook-types.md`
- Modify: `docs/03-hooks-automation/03-automation-patterns.md`
- Modify: `docs/03-hooks-automation/04-settings-config.md`

**Interfaces:**
- Consumes: Task 1-3 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの Part 3 セクション

- [ ] **Step 1: 公式ドキュメント取得（フック・設定）**

```
https://docs.anthropic.com/en/docs/claude-code/hooks
https://docs.anthropic.com/en/docs/claude-code/settings
```

- [ ] **Step 2: 01-hooks-overview.md を更新**

確認・修正ポイント:
- フックイベント一覧（PreToolUse / PostToolUse / Stop / Notification / UserPromptSubmit）が完全か
- exit code の意味（0 = 成功, 1 = ブロック等）が正確か
- フックの出力（JSON stdout）の仕様が最新か
- 設定ファイルのパス一覧が正確か

練習問題を追加（「次のステップ」直前）:
```markdown
## 🏋️ 練習問題

1. **【確認】** フックが使える5つのイベントを全て答えてください。
2. **【確認】** PreToolUse フックで exit code 1 を返すと何が起きますか？
3. **【実践】** `~/.claude/settings.json` を開き、現在のフック設定を確認しましょう（なければ空の `hooks: {}` を確認）。
4. **【応用】** ファイル編集後に自動でフォーマットを実行するフックを設定するには、どのイベントとどのマッチャーを使いますか？
```

- [ ] **Step 3: 02-hook-types.md を更新**

確認・修正ポイント:
- 各フックタイプの `matcher` フィールドの書き方が正確か
- `hooks` 配列内の `type: "command"` フィールドの仕様が最新か
- stdin 経由でのコンテキスト受け取り方が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** `PreToolUse` と `PostToolUse` の違いを説明してください。
2. **【実践】** Edit ツールの PostToolUse で `echo "file edited"` を実行するフックを設定してみましょう。
3. **【実践】** Stop イベントで通知を出すフックを作成し、動作を確認しましょう。
4. **【応用】** `Notification` フックを使って、モバイルに通知を送るにはどのようなコマンドが必要ですか？
```

- [ ] **Step 4: 03-automation-patterns.md を更新**

確認・修正ポイント:
- lint 自動実行・テスト自動実行のサンプル設定が現在の settings.json スキーマと一致しているか
- Slack 通知などの実践的なサンプルが含まれているか
- Windows 環境向けの PowerShell コマンド例が含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** フックを使って lint エラーがある場合にファイル編集をブロックするには、どのフックタイプを使いますか？
2. **【実践】** ファイル編集後に `echo "編集完了: $TOOL_INPUT_PATH"` を出力するフックを設定してみましょう。
3. **【実践】** Claude が完了した際に OS 通知（macOS: `osascript`、Windows: `msg *`）を送るフックを作成しましょう。
4. **【応用】** CI/CD 環境でフックを使う場合の注意点を2つ挙げてください。
```

- [ ] **Step 5: 04-settings-config.md を更新**

確認・修正ポイント:
- `settings.json` の完全なスキーマが最新か（`model`, `allowedTools`, `blockedTools`, `env`, `hooks` 等）
- グローバル / プロジェクト / ローカル設定の優先順位が正確か
- 権限管理（`permissions` フィールド）の説明が含まれているか
- 環境変数の設定方法が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** `~/.claude/settings.json` と `.claude/settings.json` のどちらが優先されますか？
2. **【実践】** `allowedTools` に `["Bash(git *)"]` を追加して、git コマンドを確認なしで実行できるようにしましょう。
3. **【実践】** `model` フィールドでデフォルトモデルを変更してみましょう。
4. **【応用】** チームで共有する `.claude/settings.json` に含めるべき設定と、個人の `settings.local.json` に留めるべき設定の違いを説明してください。
```

- [ ] **Step 6: コミット**

```bash
git add docs/03-hooks-automation/
git commit -m "docs(part3): 正確性更新・練習問題追加"
```

---

## Task 5: Part 4 — メモリシステム

**Files:**
- Modify: `docs/04-memory-system/01-memory-types.md`
- Modify: `docs/04-memory-system/02-claude-md.md`
- Modify: `docs/04-memory-system/03-persistent-memory.md`
- Modify: `docs/04-memory-system/04-memory-best-practices.md`

**Interfaces:**
- Consumes: Task 1-4 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの Part 4 セクション

- [ ] **Step 1: 公式ドキュメント取得（メモリ・CLAUDE.md）**

```
https://docs.anthropic.com/en/docs/claude-code/memory
https://docs.anthropic.com/en/docs/claude-code/context
```

- [ ] **Step 2: 01-memory-types.md を更新**

確認・修正ポイント:
- メモリの種類（user / feedback / project / reference）が現在の仕様と一致しているか
- 各メモリタイプのフロントマターフォーマットが正確か
- MEMORY.md インデックスファイルの役割と形式が正確か
- メモリファイルの保存パスが正確か

練習問題を追加（「次のステップ」直前）:
```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code のメモリタイプを4種類答え、それぞれの用途を説明してください。
2. **【確認】** `feedback` タイプのメモリに含めるべき情報を説明してください。
3. **【実践】** `~/.claude/projects/` ディレクトリを確認し、既存のメモリファイルを探してみましょう。
4. **【応用】** コードベースの構造やファイルパスをメモリに保存すべきでない理由を説明してください。
```

- [ ] **Step 3: 02-claude-md.md を更新**

確認・修正ポイント:
- 複数の CLAUDE.md を階層配置できる仕様が正確か
- `@import` によるファイル参照の機能が説明されているか
- `/init` コマンドの動作が最新か
- 「書くべき内容・書かない内容」の表が現在のベストプラクティスと一致しているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** CLAUDE.md はいつ Claude Code に読み込まれますか？
2. **【実践】** `/init` を使って現在のプロジェクトの CLAUDE.md を生成し、内容を確認しましょう。
3. **【実践】** CLAUDE.md に「このプロジェクトでは `console.log` をコミットしないこと」を追記して、次のセッションで有効になるか確認しましょう。
4. **【応用】** `src/` と `docs/` でそれぞれ異なる CLAUDE.md を配置するのが有効な場面を考えてみましょう。
```

- [ ] **Step 4: 03-persistent-memory.md を更新**

確認・修正ポイント:
- ファイルベースメモリの保存パスが正確か（`~/.claude/projects/<hash>/memory/`）
- セッション間でのメモリ継続の仕組みが正確か
- メモリファイルのフロントマター構造（`name`, `description`, `metadata.type`）が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** 永続メモリファイルのフロントマターに必要なフィールドを全て答えてください。
2. **【実践】** `user` タイプのメモリファイルを手動で作成し、自分の技術スタックを記録してみましょう。
3. **【実践】** 新しいセッションを開始し、前のセッションで保存したメモリが引き継がれているか確認しましょう。
4. **【応用】** メモリが古くなったとき（例：使っていたライブラリを変更したとき）はどうすべきですか？
```

- [ ] **Step 5: 04-memory-best-practices.md を更新**

確認・修正ポイント:
- 「メモリに保存すべきでない情報」のリストが網羅的か
- メモリとタスクリスト（TodoWrite）の使い分けが説明されているか
- メモリのサイズ・件数の管理に関するアドバイスが含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** コードパターン・アーキテクチャをメモリに保存しない理由を説明してください。
2. **【確認】** `feedback` メモリの `Why:` と `How to apply:` 行の役割を説明してください。
3. **【実践】** 過去の作業で「次回のセッションでも覚えておいてほしいこと」を1件メモリに保存してみましょう。
4. **【応用】** チームメンバー間でメモリを共有したい場合、どのような方法が考えられますか？
```

- [ ] **Step 6: コミット**

```bash
git add docs/04-memory-system/
git commit -m "docs(part4): 正確性更新・練習問題追加"
```

---

## Task 6: Part 5 — MCP とエージェント

**Files:**
- Modify: `docs/05-mcp-agents/01-mcp-overview.md`
- Modify: `docs/05-mcp-agents/02-mcp-servers.md`
- Modify: `docs/05-mcp-agents/03-agent-sdk.md`
- Modify: `docs/05-mcp-agents/04-multi-agent.md`
- Modify: `docs/05-mcp-agents/05-custom-agents.md`

**Interfaces:**
- Consumes: Task 1-5 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの Part 5 セクション

- [ ] **Step 1: 公式ドキュメント取得（MCP・エージェント）**

```
https://docs.anthropic.com/en/docs/claude-code/mcp
https://docs.anthropic.com/en/docs/claude-code/sdk
https://modelcontextprotocol.io/docs/concepts/architecture
```

- [ ] **Step 2: 01-mcp-overview.md を更新**

確認・修正ポイント:
- MCP のアーキテクチャ（stdio / SSE / HTTP）が最新の仕様と一致しているか
- Tools / Resources / Prompts の3種類の説明が正確か
- 公式 MCP サーバー一覧が最新か（新しいサーバーが追加されていないか）

練習問題を追加（「次のステップ」直前）:
```markdown
## 🏋️ 練習問題

1. **【確認】** MCP が提供する3種類の機能（Tools / Resources / Prompts）をそれぞれ説明してください。
2. **【確認】** 組み込みツール（Read/Edit/Bash）と MCP ツールの主な違いを2つ答えてください。
3. **【実践】** 公式の MCP サーバー一覧（modelcontextprotocol/servers）を確認し、自分のプロジェクトで使えそうなサーバーを1つ選んでみましょう。
4. **【応用】** GitHub MCP サーバーを使うことで、現在の作業フローのどの部分を改善できますか？
```

- [ ] **Step 3: 02-mcp-servers.md を更新**

確認・修正ポイント:
- settings.json での MCP サーバー設定の構文が最新か
- stdio / SSE の設定方法の違いが正確か
- 認証（環境変数での API キー渡し）の方法が安全かつ正確か
- `claude mcp add` コマンドが存在するか確認し、あれば追記

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** stdio 型と SSE 型の MCP サーバーの違いを説明してください。
2. **【実践】** `@modelcontextprotocol/server-filesystem` を settings.json に設定してみましょう。
3. **【実践】** 設定した MCP サーバーが認識されているか `claude mcp list`（または相当コマンド）で確認しましょう。
4. **【応用】** MCP サーバーの API キーを安全に管理するベストプラクティスを説明してください。
```

- [ ] **Step 4: 03-agent-sdk.md を更新**

確認・修正ポイント:
- Agent SDK の現在の API（`claude` CLI オプション、`--print`, `--output-format` 等）が正確か
- SDK での使い方（Python / TypeScript）のサンプルが現在の仕様と一致しているか
- `ClaudeCodeOptions` などの主要な型・設定が最新か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code Agent SDK でエージェントを起動する際に必要な最小限のパラメータを答えてください。
2. **【実践】** `claude --print "Hello"` を実行して、非インタラクティブモードの動作を確認しましょう。
3. **【実践】** SDK を使って、指定ファイルの要約を出力する簡単なスクリプトを書いてみましょう。
4. **【応用】** SDK でエージェントを使う場合と CLI を直接使う場合の使い分けを考えてみましょう。
```

- [ ] **Step 5: 04-multi-agent.md を更新**

確認・修正ポイント:
- マルチエージェントの並列実行パターンが現在の仕様と一致しているか
- サブエージェントの起動方法（Agent ツール）が正確か
- オーケストレーターとサブエージェントの通信方法が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** オーケストレーターとサブエージェントの役割の違いを説明してください。
2. **【確認】** サブエージェントを並列実行する場合と直列実行する場合の使い分けを答えてください。
3. **【実践】** 2つの独立したタスクを並列サブエージェントで実行するプロンプトを作成してみましょう。
4. **【応用】** マルチエージェントシステムを設計する際のリスク（コスト・エラー伝播等）を2つ挙げてください。
```

- [ ] **Step 6: 05-custom-agents.md を更新**

確認・修正ポイント:
- カスタムエージェントの設定方法（`.agents/` ディレクトリ等）が正確か
- エージェントの専門化（利用可能なツールの制限等）の方法が最新か
- `subagent_type` の設定方法が正確か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** カスタムエージェントを定義するファイルの配置場所を答えてください。
2. **【実践】** 利用可能なツールを Read / Grep のみに限定した「コードリーダー」エージェントを定義してみましょう。
3. **【実践】** 定義したカスタムエージェントを Agent ツールで呼び出してみましょう。
4. **【応用】** 専門エージェントを複数用意するメリットと、単一の汎用エージェントを使うメリットを比較してください。
```

- [ ] **Step 7: コミット**

```bash
git add docs/05-mcp-agents/
git commit -m "docs(part5): 正確性更新・練習問題追加"
```

---

## Task 7: Part 6 — 発展・応用

**Files:**
- Modify: `docs/06-advanced/01-ci-cd-integration.md`
- Modify: `docs/06-advanced/02-team-workflow.md`
- Modify: `docs/06-advanced/03-performance-tips.md`
- Modify: `docs/06-advanced/04-troubleshooting.md`
- Modify: `ROADMAP.md`（新機能追記）

**Interfaces:**
- Consumes: Task 1-6 で確立した練習問題フォーマット
- Produces: 正確化・演習追加済みの全セクション、更新済み ROADMAP.md

- [ ] **Step 1: 公式ドキュメント取得（CI/CD・トラブルシューティング）**

```
https://docs.anthropic.com/en/docs/claude-code/github-actions
https://docs.anthropic.com/en/docs/claude-code/troubleshooting
https://docs.anthropic.com/en/docs/claude-code/costs
```

- [ ] **Step 2: 01-ci-cd-integration.md を更新**

確認・修正ポイント:
- GitHub Actions での Claude Code 使用方法（`claude-code-action`）が最新か
- 非インタラクティブモード（`--print` フラグ等）の説明が正確か
- CI 用の認証方法（`ANTHROPIC_API_KEY` シークレット設定）が最新か

練習問題を追加（「次のステップ」直前）:
```markdown
## 🏋️ 練習問題

1. **【確認】** CI 環境で Claude Code を実行する際に必ず設定が必要な環境変数を答えてください。
2. **【実践】** 簡単な GitHub Actions ワークフロー（PR 作成時にコードレビューを実行）を作成してみましょう。
3. **【実践】** `claude --print "このコードを説明して"` を CI スクリプトから呼び出すサンプルを書いてみましょう。
4. **【応用】** Claude Code を CI に組み込む際のコスト管理のポイントを2つ挙げてください。
```

- [ ] **Step 3: 02-team-workflow.md を更新**

確認・修正ポイント:
- 共有 `.claude/settings.json` の管理方法が実践的か
- チームメンバーへの権限付与の方法が正確か
- PR レビュー自動化のワークフロー例が最新か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** チームで共有すべき設定ファイルと個人設定に留めるべきファイルをそれぞれ答えてください。
2. **【実践】** `.claude/settings.json` にチーム共通の `allowedTools` 設定を追加し、git にコミットしましょう。
3. **【実践】** CLAUDE.md にチームのコーディング規約を追記し、Claude Code がそれを遵守するか確認しましょう。
4. **【応用】** 新しいチームメンバーが Claude Code をセットアップする際の「オンボーディングチェックリスト」を作成してみましょう。
```

- [ ] **Step 4: 03-performance-tips.md を更新**

確認・修正ポイント:
- プロンプトキャッシングの活用方法が最新か
- コンテキストを絞り込む実践的なテクニックが含まれているか
- `/compact` の使いどころが明確か
- モデル選択（Opus vs Sonnet vs Haiku）の指針が最新か

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** Claude Code でコストを削減するために有効な方法を3つ挙げてください。
2. **【実践】** 大きなプロジェクトで作業する際に `/compact` を実行するタイミングを意識してみましょう。
3. **【実践】** `claude-haiku-4-5` を使った場合と `claude-opus-4-8` を使った場合で、同じタスクの品質とコストを比較してみましょう。
4. **【応用】** CLAUDE.md を活用してコンテキストの重複読み込みを減らす工夫を考えてみましょう。
```

- [ ] **Step 5: 04-troubleshooting.md を更新**

確認・修正ポイント:
- よくあるエラーと解決策のリストが現在のバージョンに対応しているか
- `claude doctor` の診断項目が最新か
- 認証エラー・ネットワークエラーの解決手順が正確か
- Windows 固有の問題（PowerShell・パス区切り等）への対処が含まれているか

練習問題を追加:
```markdown
## 🏋️ 練習問題

1. **【確認】** `claude doctor` で確認できる診断項目を3つ答えてください。
2. **【実践】** 意図的に `ANTHROPIC_API_KEY` を空にして Claude Code を起動し、エラーメッセージを確認しましょう（確認後は元に戻すこと）。
3. **【実践】** Claude Code が応答しなくなった場合の対処手順を実際に試してみましょう。
4. **【応用】** チームメンバーから「Claude Code が動かない」と言われた場合の診断フローを3ステップで説明してください。
```

- [ ] **Step 6: ROADMAP.md に未収録機能を追記**

Task 6（Part 5）までの公式ドキュメント調査で発見した「独立セクションが必要な未収録機能」と、Task 7 の Part 6 調査で新たに発見した機能を `ROADMAP.md` の `v0.2.0` または `v0.3.0` セクションに追記する。

確認済みの追記候補（実際に公式ドキュメントに記載があるか各タスクで確認済みのもの）:
- Plan モード（`/plan`）— インタラクティブな計画立案モード
- Worktrees 統合（`superpowers:using-git-worktrees` 相当の機能）— Git ワークツリーを使った並列作業

上記に加え、Part 6 の公式ドキュメント調査で発見した未収録の重要機能があれば同様に追記する。追記しない場合はこのステップをスキップしてよい。

- [ ] **Step 7: コミット**

```bash
git add docs/06-advanced/ ROADMAP.md
git commit -m "docs(part6): 正確性更新・練習問題追加・ROADMAP更新"
```
