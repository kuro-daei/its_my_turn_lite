# 03. プラグイン・スキル・エージェントの活用

このドキュメントでは、Claude Code の拡張機能を活用してできることの幅を広げる方法を学びます。
プラグイン・スキル・エージェント・MCP サーバーの4つの仕組みを理解し、実際に researcher/writer/reviewer という3エージェントが連携するワークフローを構築します。

---

## 目次

1. [Claude Code の拡張システム概要](#1-claude-code-の拡張システム概要)
2. [Superpowers プラグインのインストール](#2-superpowers-プラグインのインストール)
3. [MCP サーバーの設定](#3-mcp-サーバーの設定)
4. [カスタムスキルの作成](#4-カスタムスキルの作成)
5. [researcher/writer/reviewer エージェントワークフロー](#5-researcherwriterreviewer-エージェントワークフロー)

---

## この章でやること

- Claude Code のプラグイン・スキル・エージェントシステムを理解する
- Superpowers プラグインをインストールする
- MCP サーバー（Web 検索・GitHub 等）を設定する
- researcher/writer/reviewer の3エージェントワークフローを構築する

---

## 1. Claude Code の拡張システム概要

Claude Code は「4つの拡張レイヤー」で機能を拡張できます。それぞれの役割を理解することが、活用の第一歩です。

```
┌─────────────────────────────────────────────┐
│              Claude Code 拡張システム          │
├─────────────┬───────────────────────────────┤
│  プラグイン  │  スキル群・エージェント群・MCP を  │
│  (Plugin)   │  まとめてパッケージ化したもの       │
├─────────────┼───────────────────────────────┤
│  スキル     │  /コマンドで呼び出せる再利用可能な  │
│  (Skill)    │  手順書（Markdown ファイル）        │
├─────────────┼───────────────────────────────┤
│  エージェント│  役割特化した AI サブプロセス       │
│  (Agent)    │  （.claude/agents/ に配置）        │
├─────────────┼───────────────────────────────┤
│  MCP サーバー│  外部サービスとの連携              │
│  (MCP)      │  （Web 検索・GitHub・Notion 等）    │
└─────────────┴───────────────────────────────┘
```

### 各レイヤーの関係

```
プラグイン（大）
└── スキル群・エージェント群・MCP の組み合わせをまとめたパッケージ

スキル（中）
└── /コマンドで呼び出せる手順書。MCP ツールを使うものもある

エージェント（中）
└── 特定の役割に特化した AI。必要なツールと権限を定義する

MCP サーバー（基盤）
└── Web 検索・GitHub・Notion などの外部サービスへのアクセスを提供
```

**ポイント:** まず MCP でツールを整え、スキルで手順を定義し、エージェントで役割を割り当て、プラグインでまとめてインストール——という階層構造になっています。

---

## 2. Superpowers プラグインのインストール

### Superpowers とは

Superpowers は Jesse Vincent が開発した Claude Code 用プラグインで、2026年1月に公式マーケットプレイスに採用されました。
開発効率を上げるためのスキル群がまとめてインストールされます。

### インストール方法

```bash
# マーケットプレイスを追加
/plugin marketplace add obra/superpowers-marketplace

# Superpowers をインストール
/plugin install superpowers@superpowers-marketplace
```

インストール後は、以下のスキルがすぐに使えるようになります。

### 主な提供スキル

| スキル | 内容 |
|---|---|
| `/brainstorming` | 実装前に要件・設計を深掘りして整理する |
| TDD サイクル | red-green-refactor を強制して品質を保つ手順 |
| `/execute-plan` | チェックポイント付きで計画を段階実行する |
| デバッグ手順 | 根本原因調査→修正の4フェーズで問題を解決する |

### 使い方のイメージ

```
# 新機能を実装する前にブレインストーミング
「/brainstorming を使って、ユーザー認証機能の設計を整理してください」

# 計画を立てて段階的に実行
「/execute-plan を使って、README の改訂計画を実行してください」
```

---

## 3. MCP サーバーの設定

### MCP とは

MCP（Model Context Protocol）は、外部サービスと Claude を繋ぐオープンプロトコルです。
MCP サーバーを追加することで、Claude Code が Web 検索・GitHub 操作・Notion 編集などの外部サービスを直接利用できるようになります。

```
Claude Code
    │
    ├── MCP: Brave Search ─── Web 検索
    ├── MCP: GitHub ─────────  PR/Issue 操作
    └── MCP: Notion ─────────  ドキュメント管理
```

### `claude mcp add` コマンドの使い方

MCP サーバーの追加は `claude mcp add` コマンドで行います。接続方式によって `--transport` の指定が変わります。

```bash
# HTTP サーバー（外部サービスに接続するシンプルな方式）
claude mcp add --transport http <name> <url>

# stdio サーバー（ローカルで Node.js プロセスを起動する方式）
claude mcp add --transport stdio <name> -- npx -y <package>
```

### スコープの使い分け

| スコープ | オプション | 適用範囲 |
|---|---|---|
| local（デフォルト）| なし | 現在のプロジェクトのみ（自分だけ） |
| project | `--scope project` | プロジェクト全員で共有（`.claude/settings.json` に保存） |
| user | `--scope user` | 全プロジェクトで使用（`~/.claude/settings.json` に保存） |

**初心者の場合はデフォルト（local）のまま使って問題ありません。**

---

### 3-1. Web 検索（Brave Search MCP）

#### Brave Search を選ぶ理由

| 特徴 | 内容 |
|---|---|
| 無料プラン | 月2000クエリまで無料 |
| プライバシー | トラッキングなし・広告追跡なし |
| 独立インデックス | Google・Bing 非依存の独自検索結果 |

#### API キーの取得

1. [brave.com/search/api](https://brave.com/search/api) にアクセス
2. 「Get Started for Free」をクリック
3. アカウントを作成してログイン
4. API キーを発行・コピー

#### インストール

```bash
claude mcp add --transport stdio brave-search \
  --env BRAVE_API_KEY=YOUR_KEY \
  -- npx -y @modelcontextprotocol/server-brave-search
```

`YOUR_KEY` の部分を取得した API キーに置き換えてください。

---

### 3-2. GitHub MCP

GitHub との連携により、PR 管理・Issue 操作・コードレビューを Claude Code から直接実行できます。

#### インストール

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

#### できること

| 操作 | 内容 |
|---|---|
| PR 管理 | PR の作成・コメント・マージ |
| Issue 操作 | Issue の作成・更新・クローズ |
| コードレビュー | 差分の確認・レビューコメント |
| リポジトリ情報 | ファイル内容・コミット履歴の参照 |

---

### 3-3. Notion MCP

Notion のドキュメントを Claude Code から読み書きできます。

#### インストール

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

Notion の MCP を使うと、調査結果や文書のドラフトを直接 Notion に保存したり、既存のページを参照して作業したりできます。

---

### 3-4. 設定の確認

#### 登録済み MCP の一覧を見る

```bash
claude mcp list
```

出力例：

```
brave-search  stdio  npx -y @modelcontextprotocol/server-brave-search
github        http   https://api.githubcopilot.com/mcp/
notion        http   https://mcp.notion.com/mcp
```

#### `.claude/settings.json` の中身

project スコープで追加した MCP は `.claude/settings.json` に保存されます。

```json
{
  "mcpServers": {
    "github": {
      "transport": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "notion": {
      "transport": "http",
      "url": "https://mcp.notion.com/mcp"
    }
  }
}
```

> **注意:** API キーなどの秘密情報を `settings.json` に直接書かないようにしてください。`--env` フラグを使うと環境変数として安全に渡せます。

---

## 4. カスタムスキルの作成

### スキルとは

スキルは `/コマンド` で呼び出せる Markdown ファイルです。よく使う作業手順を書き留めておくことで、毎回同じ指示を打ち込まずに済みます。

```
# スキルを呼び出す例
「/summarize を使って、このファイルを要約してください」
```

### 配置場所

| 場所 | パス | 適用範囲 |
|---|---|---|
| 個人スキル | `~/.claude/skills/<name>/SKILL.md` | 全プロジェクト |
| プロジェクト | `.claude/skills/<name>/SKILL.md` | このプロジェクトのみ |

### SKILL.md の基本構造

```markdown
---
name: summarize
description: 指定されたファイルを要約する
allowed-tools: Read
---

以下の手順でファイルを要約してください：
1. ファイルを読む
2. 主要ポイントを3点に絞る
3. Markdown の箇条書きでまとめる
```

フロントマター（`---` で囲まれた部分）に必要な項目を設定します。

| 項目 | 必須 | 内容 |
|---|---|---|
| `name` | 必須 | スキル名（`/コマンド` の名前） |
| `description` | 必須 | スキルの説明（Claude がいつ使うかの判断に使う） |
| `allowed-tools` | 任意 | 使用を許可するツールのリスト |

### スキルの作成例

```bash
# プロジェクト用スキルを作成
mkdir -p .claude/skills/summarize

# SKILL.md を作成
# （エディタで .claude/skills/summarize/SKILL.md を作成）
```

---

## 5. researcher/writer/reviewer エージェントワークフロー

### ワークフローの全体像

Claude Code のエージェント機能を使うと、役割に特化した AI を複数組み合わせて、複雑な作業を自動化できます。

ここでは情報収集から文書作成・レビューまでを3つのエージェントに分担させるワークフローを構築します。

```
ユーザー
  ↓ 「〇〇を調査してまとめて」
researcher エージェント
  ↓ 調査結果を research/ フォルダに保存
writer エージェント
  ↓ research/ の内容を読んで文書を作成
reviewer エージェント
  ↓ 品質をレビューして修正指示
完成！
```

---

### 5-1. エージェントの配置場所

`.claude/agents/` ディレクトリに Markdown ファイルを置くだけで、Claude Code がエージェントとして認識します。

```
.claude/
└── agents/
    ├── researcher.md   # 情報収集専門
    ├── writer.md       # 文書作成専門
    └── reviewer.md     # 品質レビュー専門
```

```bash
# ディレクトリを作成
mkdir -p .claude/agents
```

---

### 5-2. researcher エージェントの定義

`.claude/agents/researcher.md` を以下の内容で作成します。

```markdown
---
name: researcher
description: 情報収集・調査専門エージェント。リサーチタスクを依頼されたときに使う
tools: Read, Grep, Glob, Bash, WebSearch
model: sonnet
permissionMode: plan
---

あなたは情報収集の専門家です。

## 調査手順
1. 指定されたトピックについて Web 検索・ファイル検索で情報を収集する
2. 複数の情報源を比較・検証する
3. 調査結果を `research/<日付>-<トピック>.md` に保存する

## 出力フォーマット
research/ フォルダに以下の形式で保存してください：
\`\`\`markdown
# 調査: <トピック>
調査日: <日付>

## 要約
## 詳細
## 情報源
\`\`\`
```

**フロントマターの設定ポイント:**

| 項目 | 設定値 | 意味 |
|---|---|---|
| `tools` | Read, Grep, Glob, Bash, WebSearch | 使用できるツール |
| `model` | sonnet | 使用するモデル |
| `permissionMode` | plan | 実行前に計画を提示して確認を求める |

---

### 5-3. writer エージェントの定義

`.claude/agents/writer.md` を以下の内容で作成します。

```markdown
---
name: writer
description: 文書作成専門エージェント。research/ の調査結果をもとに文書を作成する
tools: Read, Write, Glob
model: sonnet
---

あなたはテクニカルライターです。

## 作業手順
1. `research/` フォルダの最新調査ファイルを読む
2. 調査内容をもとに、読者にとって分かりやすい文書を作成する
3. Markdown 形式で、見出し・表・コードブロックを活用する
4. ファイルを `docs/` または指定の場所に保存する

## 文書品質基準
- 初心者でも理解できる説明
- ASCII 図で概念を視覚化
- コマンドは実行結果つきで掲載
```

---

### 5-4. reviewer エージェントの定義

`.claude/agents/reviewer.md` を以下の内容で作成します。

```markdown
---
name: reviewer
description: 品質レビュー専門エージェント。作成された文書の品質をチェックする
tools: Read, Glob
model: sonnet
permissionMode: plan
---

あなたはシニアテクニカルエディターです。

## レビュー観点
- 正確性：コマンドや手順に誤りがないか
- 明瞭性：初心者が理解できるか
- 完全性：重要な情報が漏れていないか
- 構成：論理的な流れになっているか

## 出力
レビュー結果を以下の形式で報告してください：
- Critical（要修正）
- Important（できれば修正）
- Minor（軽微な指摘）
```

**reviewer の `permissionMode: plan` について:** レビュアーはファイルを読むだけで書き込みは行わないため、`tools` に Write を含めていません。`permissionMode: plan` を設定することで、実行前に何をするか確認できます。

---

### 5-5. エージェントの呼び出し方

Claude Code のチャットから自然言語でエージェントを指定します。

```
# researcher に調査を依頼
「researcher エージェントを使って、Rust の所有権システムについて調査してください」

# writer に文書作成を依頼
「writer エージェントを使って、research/ の最新ファイルをもとに初心者向け解説を書いてください」

# reviewer にレビューを依頼
「reviewer エージェントを使って、docs/rust-ownership.md をレビューしてください」
```

エージェントを直接指定しなくても、`description` の内容から Claude Code が適切なエージェントを自動選択することもあります。

---

### 5-6. research/ フォルダの構成

調査結果は日付とトピックを含む名前で保存することで、後から見つけやすくなります。

```
research/
├── 2026-03-08-rust-ownership.md
├── 2026-03-09-typescript-generics.md
└── README.md  ← 調査ログ一覧
```

#### 命名規則

```
<YYYY-MM-DD>-<トピック名（英数字とハイフン）>.md
```

例: `2026-03-08-rust-ownership.md`

#### 下書きを公開しない場合

調査中の下書きをリポジトリに含めたくない場合は `.gitignore` に追加します。

```bash
# .gitignore に追記
echo "research/" >> .gitignore
```

---

## まとめ

| 機能 | 目的 | 設定場所 |
|---|---|---|
| Superpowers プラグイン | 強力なスキルをまとめてインストール | `/plugin` コマンド |
| MCP サーバー | Web 検索・GitHub・Notion 連携 | `claude mcp add` |
| カスタムスキル | `/コマンド` で手順を再利用 | `.claude/skills/` |
| エージェント | 役割特化 AI が自律的に作業 | `.claude/agents/` |

### この章で学んだこと

1. **4つの拡張レイヤー** — MCP・スキル・エージェント・プラグインの役割と関係
2. **Superpowers** — `/brainstorming` や `/execute-plan` などの実用スキルをまとめてインストール
3. **MCP サーバー** — Brave Search・GitHub・Notion を `claude mcp add` で接続
4. **カスタムスキル** — SKILL.md を配置して独自の `/コマンド` を作成
5. **3エージェントワークフロー** — researcher → writer → reviewer の連携で調査から文書完成まで自動化

---

**次の章へ:**
[04_research_with_claude.md](./04_research_with_claude.md) — Claude でディープリサーチ
