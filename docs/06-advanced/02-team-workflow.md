# 6-2: チームワークフロー

> **学習時間**: 15分 | **難易度**: ⭐⭐⭐

## チームでの Claude Code 活用

チームで Claude Code を使う際は、個人の設定とチーム共有の設定を適切に分離することが重要です。

## 設定の分離

```
my-project/
├── .claude/
│   ├── settings.json          # チームで共有（git 管理）
│   ├── settings.local.json    # 個人設定（.gitignore に追加）
│   └── skills/                # チーム共有スキル（git 管理）
│       ├── deploy-check/
│       └── release-notes/
├── CLAUDE.md                  # プロジェクト指示（git 管理）
└── .gitignore
    # .claude/settings.local.json を追加
```

| ファイル | 共有範囲 | 内容の例 |
|---------|---------|---------|
| `.claude/settings.json` | チーム全体（git 管理） | 許可コマンド・フック・チーム共通ルール |
| `.claude/settings.local.json` | 個人のみ（.gitignore） | 個人の API キー・個人の好み |
| `CLAUDE.md` | チーム全体（git 管理） | プロジェクト概要・コーディング規約 |

### .gitignore に追加

```gitignore
# Claude Code 個人設定
.claude/settings.local.json
```

## チーム共有の settings.json

```json
{
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(npm run *)",
      "Bash(npm test)"
    ],
    "deny": [
      "Bash(git push --force *)",
      "Bash(rm -rf *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run format 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

## 効果的な CLAUDE.md の書き方（チーム向け）

```markdown
# プロジェクト名

## チームルール
- PR は必ず 1 機能ずつに分割する
- マージ前に `/code-review` を実行すること
- ブランチ名は `feature/`, `fix/`, `chore/` プレフィックスを使う

## 禁止事項
- main ブランチへの直接 push
- console.log のコミット
- `any` 型の使用

## コードオーナー
- src/auth/ → @auth-team
- src/payments/ → @payments-team

## Compact instructions

When you are using compact, please focus on test output and code changes
```

> **ポイント**: CLAUDE.md は 200 行以内に収めましょう。詳細な手順は SKILL.md に切り出すとコンテキスト節約になります。

## チーム共有スキルの管理

プロジェクトスキルをリポジトリに含めることでチーム全員が同じスキルを使えます：

```
.claude/skills/
├── release-notes/
│   └── SKILL.md   # リリースノート自動生成
├── db-migration/
│   └── SKILL.md   # DB マイグレーション安全チェック
└── api-review/
│   └── SKILL.md   # API 設計レビュー
```

スキルはオンデマンドで読み込まれるため、CLAUDE.md に詳細手順を書くよりコンテキストを節約できます。

## オンボーディングへの活用

新メンバーのオンボーディングに CLAUDE.md を活用：

````markdown
## 新メンバーへ

このプロジェクトに初めて参加する場合は、以下を実行してください：

```bash
npm install
cp .env.example .env.local
npm run db:setup
npm run dev
```

Claude Code で `/init` を実行すると、プロジェクト全体の概要を説明します。
````

## レビュー文化への組み込み

### PR テンプレートに Claude Code を組み込む

```markdown
<!-- .github/pull_request_template.md -->
## チェックリスト

- [ ] `/code-review` を実行した
- [ ] `/security-review` を実行した（認証・DB 変更がある場合）
- [ ] テストを追加・更新した
- [ ] CLAUDE.md のコーディング規約を確認した
```

## チームコストの管理

チームで使用する場合は、Anthropic Console でコストを管理できます：

- **ワークスペース支出上限**: Console の Workspace 設定でチーム全体の上限を設定
- **使用量レポート**: Console の Usage ページでチームの API 使用量を確認
- **レート制限の推奨値**（ユーザー数に応じた目安）:

| チームサイズ | TPM/ユーザー | RPM/ユーザー |
|------------|------------|------------|
| 1〜5名 | 200k〜300k | 5〜7 |
| 5〜20名 | 100k〜150k | 2.5〜3.5 |
| 20〜50名 | 50k〜75k | 1.25〜1.75 |
| 50〜100名 | 25k〜35k | 0.62〜0.87 |

## 🏋️ 練習問題

1. **【確認】** チームで共有すべき設定ファイルと個人設定に留めるべきファイルをそれぞれ答えてください。なぜそのように分けるのかも説明してください。

2. **【実践】** `.claude/settings.json` にチーム共通の `allowedTools`（git コマンドと npm スクリプト）の許可設定を追加し、`deny` リストに危険なコマンドを含めて、git にコミットしてみましょう。

3. **【実践】** CLAUDE.md にチームのコーディング規約（ブランチ命名規則・コミットメッセージ規則・コードレビュールール）を追記し、Claude Code がそれを遵守するか確認しましょう。

4. **【応用】** 新しいチームメンバーが Claude Code をセットアップする際の「オンボーディングチェックリスト」を作成してみましょう。インストール方法・API キー設定・CLAUDE.md の読み方・最初に試すべきコマンドを含めてください。

## 次のステップ

→ [6-3: パフォーマンス Tips](03-performance-tips.md) に進む
