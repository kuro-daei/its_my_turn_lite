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

### 2-1. client-research フォルダに移動

```bash
cd ~/works/client-research
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
> researcher エージェントを使って、「財務諸表の基本（BS・PL・CF）」を
  初心者向けにリサーチしてまとめてほしい
```

Claude が researcher エージェントを起動し、Web 検索で情報を収集します。

```
[researcher 起動]
Web 検索: "財務諸表 基本 初心者"
Web 検索: "貸借対照表 損益計算書 キャッシュフロー計算書 違い"

調査完了。以下が概要です：

## 財務諸表の基本

財務諸表は企業の財務状況を表す3つの書類で構成されます。

- **BS（貸借対照表）**: ある時点での「資産・負債・純資産」を示す
- **PL（損益計算書）**: 一定期間の「売上・費用・利益」の流れを示す
- **CF（キャッシュフロー計算書）**: 現金の「入り」と「出」を示す

---
情報源:
- 金融庁「財務諸表の見方」
- 東京証券取引所「投資家向けガイド」
```

### 4-2. writer に文書化を依頼する

調査結果をもとに、読みやすい文書にまとめてもらいます。

```
> この調査結果をもとに、writer エージェントで
  notes/financial-statements.md にまとめてほしい
```

```
[writer 起動]
調査結果を読み込み中...

notes/financial-statements.md を作成します。よいですか？ [y/n]
```

`y` を押すと文書が生成されます。

```
作成完了: notes/financial-statements.md

# 財務諸表の基本

**調査日**: 2026-03-08

## エグゼクティブサマリー

...（文書の内容）...
```

### 4-3. reviewer に品質チェックを依頼する

作成した文書の内容を確認してもらいます。

```
> reviewer エージェントで notes/financial-statements.md をレビューして
```

```
[reviewer 起動]
notes/financial-statements.md を読み込み中...

## レビュー結果

**Critical（要確認）**
- なし

**Important（改善推奨）**
- CF の説明が短い。営業・投資・財務の3区分を追記すると良い

**Minor（軽微）**
- 「純資産」の初出時に読み仮名があると親切
```

### 4-4. フィードバックを反映する

reviewer の指摘をもとに、再度 writer に修正を依頼できます。

```
> reviewer の指摘を反映して、CF の説明を追記してほしい
```

```
[writer 起動]
notes/financial-statements.md を更新します。

CF の説明に「営業・投資・財務キャッシュフロー」の3区分を追記しました。
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
