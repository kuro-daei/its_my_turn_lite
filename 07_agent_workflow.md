# 03. エージェントワークフロー

Claude Code にはエージェント（役割特化した AI）を複数連携させる仕組みがあります。
この章では、まず環境をセットアップし（bash フェーズ）、その後 Claude の中で
researcher → reviewer というエージェントワークフローを体験します。

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
  1. research・review スキルを作る
  2. Superpowers プラグインをインストールする（/plugin）

[Claude フェーズ]
  3. /research スキルでリサーチを実行する
  4. /review でリサーチを自動化する
```

---

## 2. bash フェーズ：環境セットアップ

### 2-1. hakuhodo フォルダに移動

```bash
cd ~/works/hakuhodo
```

### 2-2. スキルを作る

Claude Code には**スキル**という仕組みがあります。
`~/.claude/skills/<スキル名>/SKILL.md` に Markdown ファイルを置くと、`/スキル名 引数` で呼び出せる独自スキルになります。

まず Claude を起動して、スキルを作ってもらいましょう。

```bash
claude
```

Claude に次のように依頼します。

```
> research スキルを作って。
  「$ARGUMENTS についてWebを検索して調査し、概要・主要ポイント・情報源をMarkdown形式でまとめる。完了後に info/ フォルダへの保存を提案する」というリサーチ用スキル
```

続けて review スキルも作ります。

```
> review スキルを作って。
  「$ARGUMENTS のファイルを読み込み、内容の正確さ・わかりやすさ・構成を確認して、Critical／Important／Minor の3段階でフィードバックをまとめる」というレビュー用スキル
```

Claude がファイルを作成したら `/exit` で一度終了します。
スキルは起動時に読み込まれるため、追加後は Claude を再起動する必要があります。

```
/exit
```

> [!NOTE]
> **スキルとは？**
> `~/.claude/skills/<スキル名>/SKILL.md` に置いた Markdown ファイルが、そのままスキルになります。
> たとえば `/research 博報堂の概要` と入力すると、`research` スキルが「博報堂の概要」を引数として受け取り、リサーチを実行します。
> カスタムコマンド（`.claude/commands/`）がプロジェクト単位の指示書なのに対し、スキルは `~/.claude/skills/` に置くことでユーザー共通の再利用可能な機能として使えます。

### 2-3. Superpowers プラグインをインストールする

このカリキュラムで使う3つのプラグインを一括インストールします。

```bash
claude plugin install -s user superpowers@claude-plugins-official
claude plugin install -s user commit-commands@claude-plugins-official
```

| プラグイン | 主な機能 | 使う章 |
|---|---|---|
| superpowers | ブレインストーミング・計画立案などの思考補助 | 随時 |
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

### 4-1. /research スキルで調査する

`/research` スキルを使ってリサーチします。

```
> /research 博報堂の企業概要と主要事業　結果は info/info_overview.md に保存して
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
info/info_overview.md に保存しました。
```

### 4-2. /review で品質チェックをする

作成した文書の内容を確認してもらいます。

```
> /review info/info_overview.md
```

```
info/info_overview.md を読み込み中...

## レビュー結果

**Critical（要確認）**
- なし

**Important（改善推奨）**
- 売上高が最新の数字か確認が必要

**Minor（軽微）**
- グループ会社の記載が少ない。主要3社を追記すると良い
```

### 4-3. フィードバックを反映する

/review の指摘をもとに修正を依頼できます。

```
> /review の指摘を反映して、主要グループ会社を追記してほしい
```

```
info/info_overview.md を更新しました。

博報堂 DY メディアパートナーズ・SIX・アドバンス・クリエーションを追記しました。
```

---

## 5. まとめ

| フェーズ | やること |
|---|---|
| bash | プラグインインストール |
| Claude 起動 | `claude` で対話モードへ |
| Claude 内 | /research → /review の流れで作業 |

スキルを使うことで、「調査 → レビュー」の流れが Claude との会話だけで完結します。次章では Git の基本概念を学びます。

---

← [前の章: Claude Code の使い方](./06_claude_usage.md) ｜ [次の章: Git の基本概念 →](./08_git_internals.md)
