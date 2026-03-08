# 補足 01. MCP（Model Context Protocol）と外部ツール連携

> **位置づけ:** この補足資料は [02_claude_usage.md](../../02_claude_usage.md) の発展内容です。
> Claude Code の基本操作に慣れてから読むことを推奨します。

---

## MCP とは

MCP（Model Context Protocol）は、Claude Code に「外部ツール・サービス」を接続するための仕組みです。

標準の Claude Code はファイルとシェルを操作できますが、
MCP を使うと以下のようなことが追加でできるようになります：

- Web を検索して最新情報を取得する
- Notion のページを読み書きする
- Gmail を確認・送信する
- データベースに接続してクエリを実行する

MCP は Anthropic が策定したオープンな標準規格であり、
サードパーティの開発者もツールを作って公開しています。

---

## 主な MCP ツール一覧

| ツール名 | 提供元 | できること |
|---|---|---|
| WebSearch | Anthropic | Google 等で Web 検索を実行 |
| WebFetch | Anthropic | 指定 URL のページ内容を取得・要約 |
| Notion | Notion | ページ一覧・読み込み・作成・更新 |
| Gmail | Google | メール確認・検索・送信 |
| GitHub | GitHub | Issue・PR の確認・作成・コメント |
| Slack | Slack | メッセージ送受信・チャンネル確認 |
| PostgreSQL | コミュニティ | DB への SQL クエリ実行 |
| Filesystem | Anthropic | 追加のファイルシステム操作 |

> **注意:** MCP ツールはそれぞれ認証設定が必要です。
> ツールに応じて API キーや OAuth 認証が求められます。
> 初心者は WebSearch と WebFetch から始めるのがおすすめです。

---

## MCP の設定ファイル

MCP の設定は `.claude/settings.json`（プロジェクト固有）または
`~/.claude/settings.json`（ユーザー共通）に記述します。

```json
{
  "mcpServers": {
    "websearch": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-websearch"],
      "env": {
        "BRAVE_API_KEY": "your-brave-api-key-here"
      }
    },
    "webfetch": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-webfetch"]
    },
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"],
      "env": {
        "NOTION_API_KEY": "secret_xxxxxxxxxxxx"
      }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxxxxxxxxx"
      }
    }
  }
}
```

> **セキュリティ注意:** API キーや認証トークンは `.claude/settings.json` に直書きせず、
> 環境変数（`.env` ファイル）から読み込む方法を推奨します。
> `settings.json` は `.gitignore` に追加して Git で管理しないようにしましょう。

---

## MCP ツールを使った会話例

WebSearch が有効な場合：

```
> Claude Code の最新バージョンを調べて、変更点を教えて

[WebSearch を実行中...]
検索クエリ: "Claude Code latest version changelog 2025"

検索結果をもとにまとめます：
Claude Code v1.x.x（2025年XX月リリース）では...
```

WebFetch が有効な場合：

```
> https://docs.anthropic.com/claude-code の内容を要約して

[WebFetch を実行中: https://docs.anthropic.com/claude-code]

ページの内容をまとめます：
Claude Code は...
```

GitHub MCP が有効な場合：

```
> このリポジトリの未対応 Issue を一覧で教えて

[GitHub を確認中...]
オープンな Issue:
  #12 - ログイン後にリダイレクトされない（優先度: 高）
  #15 - ダークモードで文字が見えない（優先度: 中）
  ...
```

---

## MCP の設定確認

```bash
# 設定されている MCP サーバーを確認
> /status

# 出力例
MCP Servers:
  ✓ websearch    （接続済み）
  ✓ webfetch     （接続済み）
  ✗ notion       （未接続 - API キーを確認してください）
  ✗ github       （未接続 - トークンを確認してください）
```

接続に失敗している場合は、API キーや設定ファイルのパスを再確認してください。

---

[補足資料一覧に戻る](../../10_supplement_index.md)
