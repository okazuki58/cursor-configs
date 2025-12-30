# コミットメッセージ生成

以下のステージされた変更から、conventional commit形式のコミットメッセージを**英語**で生成してください。

## 要件
- 形式: `{type}: {imperative verb} {what}`
- 一行で完結（50文字以内推奨）
- 動詞は命令形（add, fix, update, remove など）
- 詳細は不要。WHYが重要な場合のみ本文を追加

## 例
- `feat: add weekly report generation`
- `fix: correct date calculation in parser`
- `refactor: extract validation logic to util`

## 変更内容
{{exec:git diff --cached}}