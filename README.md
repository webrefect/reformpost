# ReformPost SaaS v4.1

リフォーム会社向け AI記事生成・WordPress連携 SaaS

## セットアップ手順

### 1. 依存関係のインストール

```bash
npm install
```

> **注意**: Node.js v22 以上が必要です（組み込み SQLite を使用）

### 2. 環境変数の設定

`.env` ファイルを編集してください：

```env
NODE_ENV=production
PORT=3000

# セキュリティキー（本番では必ず変更）
SESSION_SECRET=（64文字のランダム文字列）
ENCRYPTION_KEY=（32文字のランダム文字列）

# 管理者パスワード（デフォルト: reform2024、必ず変更）
ADMIN_PASSWORD=（強力なパスワード）

# Anthropic API キー
ANTHROPIC_API_KEY=sk-ant-api03-...
```

### 3. 起動

```bash
npm start
# または直接:
node --experimental-sqlite server/index.js
```

## アクセス

- **管理画面**: http://localhost:3000/admin
  - ID: `admin`
  - PW: `.env`の`ADMIN_PASSWORD`（デフォルト: `reform2024`）

- **クライアント画面**: http://localhost:3000/t/{tenantSlug}/

## 利用フロー

### 管理者（リフェクト側）

1. `/admin` にログイン
2. 「新規会社登録」ボタンから4ステップウィザードで登録
   - Step1: 会社名・URL識別子（tenantSlug）・プラン設定
   - Step2: WordPress設定＋接続テスト
   - Step3: 施工事例カテゴリ選択
   - Step4: 完了（専用URL・パスワードをコピーしてクライアントへ共有）

### クライアント（リフォーム会社）

1. 管理者から受け取った専用URLにアクセス
2. パスワードでログイン
3. 施工事例情報・写真を入力
4. 「AIで記事を生成する」クリック（30〜60秒）
5. 記事プレビュー・編集
6. 「WordPressに下書き保存」クリック

## データ保存場所

SQLiteデータベース: `data/reformpost.db`（自動作成）

## 技術スタック

- **Backend**: Node.js + Express
- **Database**: SQLite (Node.js v22 組み込み `node:sqlite`)
- **Frontend**: HTML/CSS/JavaScript (バニラ)
- **AI**: Anthropic Claude API (claude-3-5-sonnet-20241022)
- **認証**: bcryptjs + Cookie-based session
- **暗号化**: AES-256-CBC (WPパスワード)

## セキュリティ設定

本番デプロイ時の必須作業：

1. `.env`の`SESSION_SECRET`を64文字ランダム文字列に変更
2. `.env`の`ENCRYPTION_KEY`を32文字ランダム文字列に変更
3. `ADMIN_PASSWORD`を強力なパスワードに変更
4. HTTPS化（Let's Encrypt推奨）
5. `NODE_ENV=production`に設定

## ディレクトリ構成

```
reformpost/
├── server/
│   ├── index.js          # メインサーバー
│   ├── db/
│   │   └── database.js   # DB管理（node:sqlite 組み込み）
│   ├── routes/
│   │   ├── admin.js      # 管理者APIルート
│   │   └── client.js     # クライアントAPIルート
│   ├── middleware/
│   │   └── auth.js       # 認証ミドルウェア
│   └── utils/
│       ├── crypto.js     # 暗号化ユーティリティ
│       └── auditLog.js   # 監査ログ
├── public/
│   ├── admin/
│   │   └── index.html    # 管理者画面
│   └── client/
│       └── index.html    # クライアント画面
├── data/                 # DB自動作成（.gitignore推奨）
├── .env                  # 環境変数
└── package.json
```

## プラン

| | Standard | Premium |
|--|--|--|
| 月間生成回数 | 50回 | 200回 |
| 月額 | ¥10,000 | ¥25,000 |
