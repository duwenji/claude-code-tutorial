# ebook-output

このディレクトリには、電子書籍形式の出力を格納します。

## 生成方法（予定）

```bash
# Pandoc で PDF 生成
pandoc docs/**/*.md -o ebook-output/claude-code-tutorial.pdf

# EPUB 生成
pandoc docs/**/*.md -o ebook-output/claude-code-tutorial.epub
```
