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
```

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

## 次のステップ

→ [6-3: パフォーマンス Tips](03-performance-tips.md) に進む
