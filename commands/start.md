# 開発開始

このコマンドは、指定されたGitHub IssueのURLから開発作業を自動的に開始します。

## 引数

- `issue_url` (必須): 開発対象のGitHub IssueのURLを指定

## 実行内容

1. **Issue情報取得**: GitHub CLIを使用してIssue詳細を取得
2. **ブランチ名生成**: Issue番号とタイトルから適切なブランチ名を自動生成
3. **ブランチ作成**: 新しいブランチを作成して切り替え
4. **開発準備完了**: Issue情報を表示して開発準備完了を通知

## プロセス詳細

### ステップ1: Issue情報の取得
- URLからリポジトリ名とIssue番号を抽出
- `gh issue view` コマンドでIssue詳細を取得
- タイトル、概要、ラベル等の情報を収集

### ステップ2: ブランチ名の自動生成
以下の形式でブランチ名を生成:
```
issue-{Issue番号}-{タイトルのスラッグ化}
```

例:
- Issue #123 "ユーザー認証機能の実装" → `issue-123-user-auth-implementation`
- Issue #456 "バグ修正: ログイン時のエラーハンドリング" → `issue-456-bug-fix-login-error-handling`

### ステップ3: ブランチ作成と切り替え
- `git checkout -b {ブランチ名}` でブランチ作成
- 自動的に新しいブランチに切り替え
- ブランチ作成完了を確認

### ステップ4: 開発準備完了通知
- 作成されたブランチ名を表示
- Issue情報のサマリーを表示
- 開発開始準備完了を通知

## 使用方法

### 基本的な使用方法
1. 開発対象のIssueのURLをコピー
2. Cursorのチャットで `/start {IssueURL}` と入力
3. コマンドを選択して実行
4. 自動的にブランチが作成され、開発開始準備が完了

### 使用例
```
/start https://github.com/your-org/your-repo/issues/123
/start https://github.com/ogawakazuki/sig/issues/456
/start https://github.com/company/project/issues/789
```

### URL形式
対応するURL形式:
- `https://github.com/{owner}/{repo}/issues/{number}`
- `https://github.com/{owner}/{repo}/issues/{number}#issuecomment-{id}`

## 前提条件

- GitHub CLI (`gh`) がインストールされ、認証済みであること
- 現在のディレクトリがGitリポジトリであること
- 指定されたIssueに対してアクセス権限があること
- `main` または `develop` ブランチから実行すること

## 注意事項

- 既に同名のブランチが存在する場合は、サフィックスを追加して回避
- Issue URLが無効な場合は適切なエラーメッセージを表示
- ネットワークエラー時は再試行を促すメッセージを表示
- プライベートリポジトリの場合は適切な認証が必要

## エラーハンドリング

### よくあるエラーと対処法

1. **GitHub CLI未認証**
   ```
   エラー: GitHub CLIが認証されていません
   対処法: gh auth login を実行してください
   ```

2. **Issue URL無効**
   ```
   エラー: 無効なIssue URLです
   対処法: 正しいGitHub IssueのURLを指定してください
   ```

3. **ブランチ名重複**
   ```
   警告: ブランチが既に存在します
   対処法: issue-123-feature-name-2 として作成します
   ```

4. **権限不足**
   ```
   エラー: Issue情報を取得できません
   対処法: リポジトリへのアクセス権限を確認してください
   ```
