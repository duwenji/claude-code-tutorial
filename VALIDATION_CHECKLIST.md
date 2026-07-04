# VALIDATION CHECKLIST

## Repository health
- [ ] `README.md` のリンクが機能する
- [ ] `docs/00-COVER.md` から主要 Part に遷移できる
- [ ] `MASTER-INDEX.md` が最新

## Commands
- [ ] `npm install`
  - 依存関係をインストールする
- [ ] `npm run ebook:step1`
- [ ] `npm run ebook:step2`
- [ ] `npm run ebook:step2b`
- [ ] `npm run ebook:step3`
  - ebook 出力を生成して build エラーを確認する

## Content quality
- [ ] スキルシステム（`SKILL.md`、`/skill-creator`、組み込みスキル）が説明されている
- [ ] Hooks（PreToolUse / PostToolUse / Stop / Notification）が含まれている
- [ ] メモリシステム（`CLAUDE.md`、永続メモリ）が含まれている
- [ ] MCP・エージェント・CI/CD 統合が含まれている

## Publication
- [ ] `.github/workflows/pages.yml` がある
- [ ] `.github/workflows/validate.yml` がある
- [ ] `LICENSE`, `CONTRIBUTING.md`, `CHANGELOG.md`, `ROADMAP.md` がある
