# PUBLISHING GUIDE

## 1. ローカルで Git リポジトリを初期化する

```powershell
git init -b main
git add .
git commit -m "Initial tutorial scaffold"
```

## 2. GitHub に新規リポジトリを作る

### 方法 A: GitHub Web
1. GitHub で新しい repository を作成する
2. `Repository name` を `claude-code-tutorial` にする
3. README や `.gitignore` は **追加しない**

### 方法 B: GitHub CLI
```powershell
gh repo create claude-code-tutorial --public --source . --remote origin --push
```

## 3. origin を追加して push する

```powershell
git remote add origin https://github.com/duwenji/claude-code-tutorial.git
git push -u origin main
```

## 4. GitHub Pages を有効化する

1. GitHub の `Settings` → `Pages`
2. `Build and deployment` を `GitHub Actions` にする
3. `pages.yml` の成功を確認する

## 5. 公開前の確認

```powershell
npm run ebook:step1
npm run ebook:step2
npm run ebook:step2b
npm run ebook:step3
```

- `README.md` のリンク確認
- `docs/00-COVER.md` の表示確認
- `ebook-output/claude-code-tutorial.epub` の生成確認
- `ebook-output/claude-code-tutorial.pdf` の生成確認
- `ebook-output/claude-code-tutorial-kdp-registration.md` の生成確認

## 6. 公開後にやること

- GitHub Pages URL を `README.md` に追加する
- Release を作成する
- 必要なら badge を追加する
