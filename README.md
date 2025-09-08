## 始め方

1. プロジェクト直下に .cursor を追加：
```
git clone git@github.com:yourname/cursor-configs.git .cursor
```

2. .gitignore に追加
プロジェクトの .gitignore に次を追記：
```
.cursor/
```

## 使い方

Cursorのチャットで /{filename} を実行

引数をつけることができるコマンドもあるので、詳しくはファイルの中身を確認してください

## 更新方法

親リポジトリにいる状態で：
```
cd .cursor && git pull origin main
```
