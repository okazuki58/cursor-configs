# プロジェクトサマリー投稿

このコマンドは、プロジェクト全体の概要を自動生成し、Slackにファイルとして投稿します。

## 実行内容（必ず3ステップすべてを実行すること）

1. プロジェクト全体を解析し、`summary.md` を生成
2. 生成したファイルをSlack Web APIでファイルとして投稿
3. 投稿結果を表示

**重要**: ステップ1だけで終わらせず、必ずステップ2（Slack投稿）まで実行してください。

---

## ステップ1: summary.md 生成

以下の内容を含む `summary.md` を生成してください：

### 含めるべき内容

**1. 何をやってるか**
- プロジェクトの目的を2-3行で説明

**2. 使ってる技術**
- フレームワーク・言語
- データベース・ORM
- 主要ライブラリ
- インフラ

**3. いまの状況**
- 実装済み機能
- 現在進行中の作業

### フォーマット

```
【プロジェクト】[プロジェクト名]

■ 何をやってるか
[2-3行で説明]

■ 技術
・フレームワーク: 
・言語: 
・データベース: 
・UI: 
・認証: 
・インフラ: 

■ いまの状況
・[機能1]
・[機能2]
→ [現在の進捗]

---
リポジトリ: [git remote -v で取得したURL]

【このコマンドの使い方】
1. Cursorで `/summary` と入力
2. AIが自動でプロジェクトを分析
3. Slackに投稿完了
```

### 生成時の注意点

- **リポジトリURLは `git remote -v` で実際のURLを取得する**（プレースホルダー禁止）
- AIモデル名など不明な情報は推測せず省略する
- カジュアルでわかりやすい表現にする
- Slack用なので【】や■などプレーンテキスト記号を使う

---

## ステップ2: Slack投稿（必須）

以下のコマンドを実行して、`summary.md` をSlackにファイルとして投稿してください：

```bash
cd /Users/ogawakazuki/Downloads/新規事業/atoyoro && \
source .env.local && \
PROJECT_NAME=$(basename $(pwd)) && \
USER_NAME=$(git config user.name || echo "Unknown User") && \
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S') && \
FILENAME="summary_${PROJECT_NAME}_$(date '+%Y%m%d_%H%M%S').md" && \
FILE_SIZE=$(wc -c < summary.md | tr -d ' ') && \
COMMENT="【プロジェクトサマリー更新】
プロジェクト: ${PROJECT_NAME}
実行者: ${USER_NAME}
日時: ${TIMESTAMP}" && \
UPLOAD_RESPONSE=$(curl -s -X POST https://slack.com/api/files.getUploadURLExternal \
  -H "Authorization: Bearer ${SLACK_BOT_TOKEN}" \
  -F "filename=${FILENAME}" \
  -F "length=${FILE_SIZE}") && \
UPLOAD_URL=$(echo "$UPLOAD_RESPONSE" | jq -r '.upload_url') && \
FILE_ID=$(echo "$UPLOAD_RESPONSE" | jq -r '.file_id') && \
curl -s -X POST "$UPLOAD_URL" -F "file=@summary.md" > /dev/null && \
curl -s -X POST https://slack.com/api/files.completeUploadExternal \
  -H "Authorization: Bearer ${SLACK_BOT_TOKEN}" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d "$(jq -n --arg file_id "$FILE_ID" --arg filename "$FILENAME" --arg channel_id "$SLACK_CHANNEL_ID" --arg comment "$COMMENT" '{files: [{id: $file_id, title: $filename}], channel_id: $channel_id, initial_comment: $comment}')" | jq -r '.files[0].permalink'
```

**使用API**: Slack Web API (`files.getUploadURLExternal` + `files.completeUploadExternal`)

---

## ステップ3: 結果表示

投稿成功後、以下を表示してください：

```
✅ プロジェクトサマリーをSlackに投稿しました！

📄 ファイル名: summary_atoyoro_YYYYMMDD_HHMMSS.md
🔗 URL: [Slack ファイルURL]
📊 サイズ: [ファイルサイズ] bytes
```

---

## 前提条件

- `.env.local` に `SLACK_BOT_TOKEN` と `SLACK_CHANNEL_ID` が設定されていること
- Slack Appに `files:write` と `chat:write` スコープが付与されていること
- プロジェクトルートディレクトリで実行すること
