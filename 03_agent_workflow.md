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

> [!NOTE]
> **補足資料:** カスタムコマンドの詳しい書き方は [supplement/claude/04_custom_commands.md](./supplement/claude/04_custom_commands.md) を参照してください。

---

## 1. この章でやること

```
[bash フェーズ]
  1. research カスタムコマンドを作る
  2. Superpowers プラグインをインストールする（/plugin）

[Claude フェーズ]
  3. /research コマンドでリサーチを実行する
  4. writer → reviewer でリサーチを自動化する
```

---

## 2. bash フェーズ：環境セットアップ

### 2-1. hakuhodo フォルダに移動

```bash
cd ~/works/hakuhodo
```

### 2-2. research カスタムコマンドを作る

Claude Code には**カスタムコマンド**という仕組みがあります。
`~/.claude/commands/` フォルダに Markdown ファイルを置くと、`/ファイル名` で呼び出せる独自コマンドになります。

まず Claude を起動して、コマンドを作ってもらいましょう。

```bash
claude
```

Claude に次のように依頼します。

```
> ~/.claude/commands/research.md を作って。
  内容は「$ARGUMENTS についてWebを検索して調査し、概要・主要ポイント・情報源をMarkdown形式でまとめる。完了後に notes/ フォルダへの保存を提案する」というリサーチ用コマンド
```

Claude がファイルを作成したら `/exit` で一度終了します。

```
/exit
```

> [!NOTE]
> **カスタムコマンドとは？**
> `$ARGUMENTS` はコマンド実行時に渡したテキストに置き換えられます。
> たとえば `/research 博報堂の概要` と入力すると、`$ARGUMENTS` の部分が「博報堂の概要」になります。
> CLAUDE.md と同じように「Markdown ファイルを置くだけ」で機能を追加できるのがポイントです。

### 2-4. Superpowers プラグインをインストールする

このカリキュラムで使う3つのプラグインを一括インストールします。

```bash
claude plugin install -s user superpowers@claude-plugins-official
claude plugin install -s user commit-commands@claude-plugins-official
claude plugin install -s user code-review@claude-plugins-official
```

| プラグイン | 主な機能 | 使う章 |
|---|---|---|
| superpowers | researcher・writer・reviewer エージェント | 03・04章 |
| commit-commands | コミット・PR 作成の補助 | 05〜12章 |
| code-review | PR レビューの補助 | 11章 |

---

## 3. Claude 起動

```bash
claude
```

```
╭──────────────────────────────────────────╮
│ Claude Code  v1.x.x                      │
│ ✓ Plugin: superpowers                    │
│ ✓ Plugin: commit-commands                │
│ ✓ Plugin: code-review                    │
╰──────────────────────────────────────────╯

>
```

MCP と Plugin が `✓` になっていれば準備完了です。

---

## 4. Claude の中での作業

### 4-1. /research コマンドで調査する

先ほど作った `/research` コマンドを使ってリサーチします。

```
> /research 博報堂の企業概要と主要事業
```

Claude が Web 検索を実行し、概要・主要ポイント・情報源をまとめたレポートを生成します。

```
[/research 実行中]
Web 検索: "博報堂 企業概要 事業内容"
Web 検索: "博報堂 グループ会社 売上高"

## 博報堂 調査レポート

**調査日**: 2026-03-09

### 概要

博報堂は、電通と並ぶ日本の大手広告会社です。...

### 主要ポイント
- 設立: 1895年（明治28年）
- 本社: 東京都港区
- 主要事業: 広告・マーケティング・PR・デジタル

### 情報源
- 博報堂 公式サイト
- 博報堂 IR ページ

---
notes/ フォルダに保存しますか？
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
| bash | Superpowers プラグインインストール |
| Claude 起動 | `claude` で対話モードへ |
| Claude 内 | researcher → writer → reviewer の流れで作業 |

エージェントを使うことで、「調査 → 文書化 → レビュー」の流れが Claude との会話だけで完結します。次章ではこのリサーチをさらに深める方法を学びます。

---

← [前の章: Claude Code の使い方](./02_claude_usage.md) ｜ [次の章: Claude でリサーチ →](./04_research_with_claude.md)
