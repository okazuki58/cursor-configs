
# コードリファクタリング (自動化 + 手動改善)

プロジェクト全体を対象に、コードのリファクタリングを効率的に進める手順：

## 自動リファクタリング

```bash
# インポート順や未使用変数の整理
npx eslint . --fix --ext .ts

# prettier でフォーマット統一
npx prettier --write "src/**/*.{ts,tsx}"
```

## 型・構造のリファクタリング

```bash
# 型の再チェック（型ガードやユーティリティ関数共通化の余地を確認）
npx tsc --noEmit
```

## 手動リファクタリングが必要な主なポイント

### 1. ガード節の共通化

* `if (!("actions" in body) || !("view" in body)) return;`
* `if (!action || !("value" in action)) return;`
  → 型ガード関数 (`guards.ts`) にまとめて再利用可能にする。

### 2. 責務の分離

* **Slackハンドラー**は「入力チェック」「データ抽出」「モーダル更新」に分割。
* `parseSelectedData` / `createSelectedDataModal` / `updateSlackModal` のテスト可能性を高める。

### 3. エラーハンドリング強化

* `console.error` のみではなく、Slack API エラーや `body.view.id` を含む詳細ログを出す。
* 将来的にはログ基盤や通知（Sentry, Slack通知）連携を検討。

### 4. スプレッド構文によるシンプル化

```ts
const updatedModalView = {
  ...createSelectedDataModal(body.view.callback_id, selectedData),
  private_metadata: body.view.private_metadata ?? "",
};
```

### 5. テストカバレッジの拡張

* 型ガード関数やユーティリティ関数に単体テストを追加。
* モーダル更新処理のモックテストを作成。

## 最終確認

```bash
# リファクタリング後の一括チェック
npm run lint && npx prettier --check "src/**/*.{ts,tsx}" && npx tsc --noEmit && npm test
```

---

**改善方針**

* 冗長なガード節は型ガード関数に共通化
* 関数責務を分けて可読性とテスト性を確保
* ログ・エラーハンドリングの強化
* プロジェクト全体でスタイルと構造を統一
