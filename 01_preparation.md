# 01. 事前準備

> [!NOTE]
> **この資料について**: 事前準備および保守用の参照資料です。ハンズオン当日は、この資料を閲覧できる状態で参加してください。

**ゴール**: Claude Code でコードが書ける状態にする
**対象者**: PC 操作に慣れた非エンジニア（ターミナルは少し使える程度）
**形式**: メンター付きハンズオン（座学ほぼなし）

---

## あなたの OS を選んでください

| OS | セットアップガイド |
|---|---|
| Mac | [→ Mac のセットアップ手順](02_preparation_mac.md) |
| Windows | [→ Windows のセットアップ手順](03_preparation_windows.md) |

---

## 事前学習（ハンズオン前に見ておこう）

インストール後や当日前日に視聴しておくと、ハンズオンがスムーズに進みます。
「ターミナルって何？」「Git って何？」という疑問を事前に解消しておきましょう。

### ターミナル・Linux コマンド

ハンズオンでは黒い画面（ターミナル）にコマンドを打ち込む操作が頻出します。
事前に「コマンドとはなにか」のイメージをつかんでおくと理解が早くなります。

| 動画 | ポイント |
|---|---|
| [Linux コマンドとターミナルと仲良くなる勉強会（YouTube）](https://www.youtube.com/watch?v=M5JCfGttqno) | CTO・VPoE によるハンズオン勉強会の録画。「ターミナルとは何か」から基本コマンドまで約 30 分で解説 |

> [!TIP]
> **最低限知っておくとよいコマンド**: `cd`（移動）、`ls`（一覧）、`mkdir`（フォルダ作成）、`pwd`（現在地確認）

### Git / GitHub

聞いたことも、なんとなくの概念も知ってると思いますが、改めてしっかり学習しましょう。

| リソース | ポイント |
|---|---|
| [Git・GitHub 入門（YouTube）](https://youtu.be/LDOR5HfI_sQ?si=7Fk-xOXzeokFn4d2) | Git と GitHub の基礎を動画でしっかり学べる |
| [初心者必読！GitHubの使い方を徹底解説【完全網羅版】](https://www.creativevillage.ne.jp/category/topcreators/web-creator/web-programmer/128504/) | リポジトリ作成からブランチ・マージまで、図解つきで網羅した記事 |

> [!TIP]
> **確認ポイント**: リポジトリ、コミット、プッシュ の 3 つの言葉の意味がわかれば OK

### Pull Request & Review

Pull Request（プルリクエスト、通称「ぷるりく」）は、「この変更をプロジェクトに取り込んでください」と提案する仕組みです。チームで開発するときの「確認・承認フロー」にあたります。

| リソース | ポイント |
|---|---|
| [Pull Request 入門（YouTube）](https://www.youtube.com/watch?v=zeX2KASkOXY) | PR の基本的な流れをわかりやすく解説 |
| [GitHub Flow でのレビュー（YouTube）](https://youtu.be/euK7YazfN3w?si=AQolTtOoYHHYa6lh) | レビューの流れと実際の操作を解説 |
| [プルリクエストの出し方・読み方（Qiita）](https://qiita.com/obscure723/items/5265556d1b89e77c456b) | PR のマナーと読み方をまとめた記事 |

### Claude Code Tips

全部を覚える必要はありません。「こういう情報がある」と把握しておくだけで、詰まったときに調べる手がかりになります。

| リソース | ポイント |
|---|---|
| [Claude Code 公式ドキュメント（日本語）](https://code.claude.com/docs/ja/overview) | 機能・コマンド・設定の公式リファレンス |

---

### 学習の進め方

動画・記事を読んだ後は、[NotebookLM](https://notebooklm.google.com/)（Google の AI ツール）に取り込んで QA を出してもらうと理解が深まります。受け身で読むだけでなく、自分の疑問に答える形で学ぶのがおすすめです。

---

[← プロローグ](00_prologue.md) | [次の章: Bash 基礎 →](04_bash_basics.md)
