# 03. エージェントワークフロー

Claude Code にはエージェント（役割特化した AI）を複数連携させる仕組みがあります。
この章では、まず環境をセットアップし（bash フェーズ）、その後 Claude の中で
researcher → writer → reviewer というエージェントワークフローを体験します。

---

## 目次

1. [この章でやること](#1-この章でやること)
2. [bash フェーズ：環境セットアップ](#2-bash-フェーズ環境セットアップ)
3. [Claude 起動](#3-claude-起動)
4. [Claude の中での作業](#4-claude-の中での作業)
5. [まとめ](#5-まとめ)

---

## 1. この章でやること

```
[bash フェーズ]
  1. Web 検索 MCP を追加する（claude mcp add）
  2. Superpowers プラグインをインストールする（/plugin）

[Claude フェーズ]
  3. researcher → writer → reviewer でリサーチを自動化する
```

---

## 2. bash フェーズ：環境セットアップ

### 2-1. hakuhodo フォルダに移動

```bash
cd ~/works/hakuhodo
```

### 2-2. Web 検索 MCP を追加する

Claude が Web 検索できるようにするため、Brave Search MCP を追加します。

```bash
claude mcp add --transport stdio brave-search \
  --env BRAVE_API_KEY=YOUR_KEY \
  -- npx -y @modelcontextprotocol/server-brave-search
```

`YOUR_KEY` は 00 章で取得した Brave Search の **Subscription Token** に置き換えてください。
（取得方法は [00_preparation_windows.md § 9](./00_preparation_windows.md) または [00_preparation_mac.md § 10](./00_preparation_mac.md) を参照）

追加後に確認：

```bash
claude mcp list
```

```
brave-search  stdio  npx -y @modelcontextprotocol/server-brave-search
```

### 2-3. Superpowers プラグインをインストールする

Superpowers は researcher・writer・reviewer などのエージェントをまとめてインストールできるプラグインです。

```bash
claude --dangerously-skip-permissions \
  -p "/plugin marketplace add obra/superpowers-marketplace && \
      /plugin install superpowers@superpowers-marketplace"
```

> **補足:** `--dangerously-skip-permissions` は非対話的なセットアップコマンドを実行するためのオプションです。
> 内容が明確なセットアップ操作のみに使用してください。

インストールが完了すると、以下のエージェントが使えるようになります：

| エージェント | 役割 |
|---|---|
| researcher | Web 検索・情報収集・要約 |
| writer | 調査結果をもとに文書を作成 |
| reviewer | 作成した文書の品質チェック |

---

## 3. Claude 起動

```bash
claude
```

```
╭──────────────────────────────────────────╮
│ Claude Code  v1.x.x                      │
│ ✓ MCP: brave-search                      │
│ ✓ Plugin: superpowers                    │
╰──────────────────────────────────────────╯

>
```

MCP と Plugin が `✓` になっていれば準備完了です。

---

## 4. Claude の中での作業

### 4-1. researcher に調査を依頼する

まずリサーチしたいテーマを伝えます。

```
> researcher エージェントを使って、「博報堂の企業概要と主要事業」を
  初心者向けにリサーチしてまとめてほしい
```

Claude が researcher エージェントを起動し、Web 検索で情報を収集します。

```
[researcher 起動]
Web 検索: "博報堂 企業概要 事業内容"
Web 検索: "博報堂 グループ会社 売上高"

調査完了。以下が概要です：

## 博報堂 企業概要

博報堂は、電通と並ぶ日本の大手広告会社です。

- **設立**: 1895年（明治28年）
- **本社**: 東京都港区
- **主要事業**: 広告・マーケティング・PR・デジタル

---
情報源:
- 博報堂 公式サイト
- 博報堂 IR ページ
```

### 4-2. writer に文書化を依頼する

調査結果をもとに、読みやすい文書にまとめてもらいます。

```
> この調査結果をもとに、writer エージェントで
  info/overview.md にまとめてほしい
```

```
[writer 起動]
調査結果を読み込み中...

info/overview.md を作成します。よいですか？ [y/n]
```

`y` を押すと文書が生成されます。

```
作成完了: info/overview.md

# 博報堂 企業概要

**調査日**: 2026-03-09

## エグゼクティブサマリー

...（文書の内容）...
```

### 4-3. reviewer に品質チェックを依頼する

作成した文書の内容を確認してもらいます。

```
> reviewer エージェントで info/overview.md をレビューして
```

```
[reviewer 起動]
info/overview.md を読み込み中...

## レビュー結果

**Critical（要確認）**
- なし

**Important（改善推奨）**
- 売上高が最新の数字か確認が必要

**Minor（軽微）**
- グループ会社の記載が少ない。主要3社を追記すると良い
```

### 4-4. フィードバックを反映する

reviewer の指摘をもとに、再度 writer に修正を依頼できます。

```
> reviewer の指摘を反映して、主要グループ会社を追記してほしい
```

```
[writer 起動]
info/overview.md を更新します。

博報堂 DY メディアパートナーズ・SIX・アドバンス・クリエーションを追記しました。
```

---

## 5. まとめ

| フェーズ | やること |
|---|---|
| bash | MCP 追加・プラグインインストール |
| Claude 起動 | `claude` で対話モードへ |
| Claude 内 | researcher → writer → reviewer の流れで作業 |

エージェントを使うことで、「調査 → 文書化 → レビュー」の流れが Claude との会話だけで完結します。次章ではこのリサーチをさらに深める方法を学びます。

---

**次の章へ:**
[04_research_with_claude.md](./04_research_with_claude.md) — Claude でディープリサーチ
