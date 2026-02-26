<p align="left">
  <a href="README_en.md"><img src="https://img.shields.io/badge/English Mode-blue.svg" alt="English"></a>
  <a href="README.md"><img src="https://img.shields.io/badge/日本語 モード-red.svg" alt="日本語"></a>
</p>

# OneNote MCP Server

Claude などの AI 言語モデルやその他の LLM が Microsoft OneNote と連携できるようにする、Model Context Protocol（MCP）サーバー実装です。

> このプロジェクトは、Zubeid Hendricks 氏による [azure-onenote-mcp-server](https://github.com/ZubeidHendricks/azure-onenote-mcp-server) をベースに、認証の簡素化と使いやすさ向上のための変更を加えたものです。

## これは何をするものですか？

このサーバーを使うと、AI アシスタントは次のことができます。
- OneNote のノートブック、セクション、ページにアクセスする
- ノートブック内に新しいページを作成する
- ノートを検索する
- HTML フォーマットとテキストを含むノート本文を読み取る
- ノート内容を直接分析・要約する

これらをすべて、コンテキストを切り替えることなく AI インターフェース上で実行できます。

## AI アシスタントでの利用

### Cursor での設定

1. このリポジトリを clone し、以下のインストール手順を実行します
2. MCP サーバーを起動します: `npm start`
3. Cursor にサーバーを登録します:
   - Cursor の設定を開く（Mac: Cmd+, / Windows: Ctrl+,）
   - `MCP` タブへ移動
   - 次の設定で MCP サーバーを追加
     - Name: `onenote`
     - Command: `node`
     - Args: `["/path/to/your/onenote-mcp.mjs"]`（絶対パスを使用）

   完全な JSON 設定例:
   ```json
   {
     "mcpServers": {
       "onenote": {
         "command": "node",
         "args": ["/absolute/path/to/your/onenote-mcp.mjs"],
         "env": {}
       }
     }
   }
   ```

4. Cursor を再起動します
5. Cursor 上で自然言語により OneNote データを操作できます:

```
Can you show me my OneNote notebooks?
Create a new page in my first notebook with a summary of this conversation
Find notes related to "project planning" in my OneNote
```

OneNote について最初に質問した際、AI が認証手順を案内します。

### Claude Desktop（または MCP 対応アシスタント）での設定

1. このリポジトリを clone し、以下のインストール手順を実行します
2. MCP サーバーを起動します: `npm start`
3. Claude Desktop の設定で OneNote MCP サーバーを追加します:
   - Name: `onenote`
   - Command: `node`
   - Args: `["/path/to/your/onenote-mcp.mjs"]`（絶対パスを使用）

   JSON 設定例:
   ```json
   {
     "mcpServers": {
       "onenote": {
         "command": "node",
         "args": ["/absolute/path/to/your/onenote-mcp.mjs"],
         "env": {}
       }
     }
   }
   ```

4. Claude に OneNote データの操作を依頼できるようになります

### GitHub Copilot（VS Code 拡張）での設定

1. このリポジトリを clone し、以下のインストール手順を実行します
2. このプロジェクトの `.vscode/mcp.json` を作成または更新します:
   ```json
   {
     "servers": {
       "onenote": {
         "command": "node",
         "args": ["/absolute/path/to/your/onenote-mcp.mjs"],
         "env": {}
       }
     }
   }
   ```
3. VS Code で `.vscode/mcp.json` を開き、MCP サーバーの `Start` ボタンを押します
4. Copilot Chat を開いて `Agent` モードに切り替え、ツールアイコンから `onenote` ツールが利用可能か確認します
5. 自然言語で OneNote 操作を依頼します

### Codex CLI（WSL 環境）での設定

1. このリポジトリを clone し、以下のインストール手順を実行します
2. WSL から Codex CLI に MCP サーバーを登録します:
   ```bash
   codex mcp add onenote -- node /mnt/c/junichi.takeda/source/onenote-mcp/onenote-mcp.mjs
   ```
3. 登録状態を確認します:
   ```bash
   codex mcp list
   ```
4. Codex CLI を起動して OneNote 関連の依頼を行うと、MCP サーバープロセスは自動起動されます

## 主な機能

- デバイスコードフローによる Microsoft OneNote 認証（Azure 設定不要）
- すべてのノートブック、セクション、ページの一覧取得
- HTML コンテンツ付きの新規ページ作成
- HTML フォーマットを含むページ本文の完全取得
- AI 分析・要約向けのテキスト抽出
- 全ページ内容の一括要約
- 全ページ本文の読みやすい形式での一括取得
- ノート全体の検索

## インストール

### 前提条件

- Node.js 16 以上（[nodejs.org](https://nodejs.org/) からインストール）
- OneNote にアクセスできる Microsoft アカウント
- Git（[git-scm.com](https://git-scm.com/) からインストール）

### 手順 1: リポジトリを clone

```bash
git clone https://github.com/yourusername/onenote-mcp.git
cd onenote-mcp
```

### 手順 2: 依存関係をインストール

```bash
npm install
```

### 手順 3: MCP サーバーを起動

```bash
npm start
```

起動すると次のメッセージが表示されます:
```
Server started successfully.
Use the "authenticate" tool to start the authentication flow,
or use "saveAccessToken" if you already have a token.
```

### 手順 4: AI アシスタント経由で認証

サーバー起動後、AI アシスタント経由で直接認証できます。

1. Cursor、Claude Desktop、または任意の MCP 対応アシスタントで、OneNote 認証を依頼します:
   ```
   Can you authenticate with my OneNote account?
   ```

2. AI が認証フローを開始し、次を案内します:
   - URL（通常は microsoft.com/devicelogin）
   - 入力用コード

3. URL にアクセスし、コードを入力して Microsoft アカウントでサインインします

4. 認証が成功したら、AI アシスタント経由で OneNote を利用できます

## 利用可能な MCP ツール

認証後、AI アシスタントから次のツールを利用できます。

| ツール名 | 説明 |
|-----------|-------------|
| `authenticate` | Microsoft 認証フローを開始 |
| `listNotebooks` | OneNote ノートブック一覧を取得 |
| `getNotebook` | 特定ノートブックの詳細を取得 |
| `listSections` | ノートブック内のセクション一覧を取得 |
| `listPages` | セクション内のページ一覧を取得 |
| `getPage` | HTML フォーマットを含むページ本文を完全取得 |
| `createPage` | HTML コンテンツ付きの新規ページを作成 |
| `searchPages` | ノートブック全体からページを検索 |

## 利用例

AI アシスタント経由で OneNote MCP を利用する際の例です。

```
User: Can you show me my OneNote notebooks?
AI: (uses listNotebooks) I found 3 notebooks: "Work", "Personal", and "Projects"

User: What sections are in my Projects notebook?
AI: (uses listSections) Your Projects notebook has the following sections: "Active Projects", "Ideas", and "Completed"

User: Create a new page in Projects with today's date as the title
AI: (uses createPage) I've created a new page titled "2025-04-12" in your Projects notebook

User: Find all my notes about machine learning
AI: (uses searchPages) I found 5 pages with content related to machine learning...

User: Can you read and summarize my notes on the "Project Requirements" page?
AI: (uses getPage) Based on your "Project Requirements" page, here's a summary: The project requires Python 3.8+, integration with AWS services, and completion by Q3. Key deliverables include a web dashboard, API, and documentation...

User: Extract all the action items from my "Team Meeting" notes
AI: (uses getPage) Here are all the action items from your "Team Meeting" notes:
1. John to complete API documentation by Friday
2. Sarah to schedule design review meeting
3. Team to finalize Q3 roadmap by end of month

User: Summarize content of all my OneNote pages
AI: (runs get-all-page-contents.js) Here's a summary of all your pages:
- Questions: Contains strategic business questions about competitor analysis
- 2025-04-12: Discussion about monetization strategy for bank transfers
- Role Specification: Details about the Chief Payments Officer position
...

User: I want to read through all my OneNote pages so I can ask questions about them
AI: (runs read-all-pages.js) I've retrieved the full content of all your pages in a readable format. Now you can ask me specific questions about any of the content.
```

## 応用: スクリプトを直接実行

テストや開発用途では、同梱スクリプトを直接実行することもできます。

```bash
# Microsoft 認証
npm run auth

# ノートブック一覧
npm run list-notebooks

# 先頭ノートブックのセクション一覧
npm run list-sections

# 先頭セクションのページ一覧
npm run list-pages

# 新規ページ作成
npm run create-page

# 全ページ内容を要約
node get-all-page-contents.js

# 全ページ本文を取得
node read-all-pages.js
```

## トラブルシューティング

### 認証関連の問題

- 認証に失敗する場合は、トラッキング防止機能のない最新ブラウザを使用してください
- ブラウザの Cookie とキャッシュのクリアを試してください
- `expired_token` エラーが出る場合は、認証プロセスを最初からやり直してください

### サーバーが起動しない

- Node.js がインストール済みか確認（16 以上）: `node --version`
- 依存関係がすべてインストールされているか確認: `npm install`
- TypeScript SDK が正しくビルドされているか確認

### AI がサーバーに接続できない

- MCP サーバーが起動していることを確認（`npm start`）
- AI アシスタント側で MCP 利用設定が有効か確認
- Cursor の場合は MCP 対応の最新版を使用

## セキュリティに関する注意

- 認証トークンはローカルの `.access-token.txt` に保存されます
- トークンには OneNote データへのアクセス権があるため安全に管理してください
- トークンは一定時間後に期限切れとなり、再認証が必要です
- Azure 設定や API キーは不要です

## クレジット

このプロジェクトは Zubeid Hendricks 氏の [azure-onenote-mcp-server](https://github.com/ZubeidHendricks/azure-onenote-mcp-server) を基に、認証フローの簡素化と AI アシスタント利用時の体験向上に焦点を当てて作成されています。

## ライセンス

このプロジェクトは MIT License のもとで提供されています。詳細は LICENSE ファイルを参照してください。
