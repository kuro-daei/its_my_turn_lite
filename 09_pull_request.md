# 09. Pull Request ワークフロー

Pull Request（PR）は、チームで安全にコードを変更するための仕組みです。gh CLI を使うと、ブラウザを開かずにターミナルだけで PR の作成・レビュー・マージまで完結できます。

---

## 目次

1. [Pull Request とは](#1-pull-request-とは)
2. [gh pr create の全オプション](#2-gh-pr-create-の全オプション)
3. [PR のレビュー](#3-pr-のレビュー)
4. [gh pr merge の使い方](#4-gh-pr-merge-の使い方)
5. [PR のベストプラクティス](#5-pr-のベストプラクティス)
6. [gh CLI 便利コマンド一覧](#6-gh-cli-便利コマンド一覧)

---

## 1. Pull Request とは

### 1-1. PR の役割

Pull Request とは、あるブランチの変更を別のブランチ（通常は `main`）に取り込むよう依頼する仕組みです。

```
feature/login ──── [commit A] ──── [commit B]
                                        ↓
                              PR を作成して main へ取り込み依頼
main ──────────────────────────────────────────
```

PR を通じて行われる「コードレビュー」には、以下の目的があります。

| 目的 | 説明 |
|------|------|
| バグの早期発見 | 複数の目でコードを確認することで、見落としを防ぐ |
| 知識の共有 | チームメンバー全員がどんな変更が加わったかを把握できる |
| 品質の維持 | コーディング規約やベストプラクティスの確認ができる |
| 安全なデプロイ | main ブランチに直接 push せず、レビュー済みのコードだけを取り込む |

### 1-2. PR の基本的な流れ

```
1. feature ブランチを作成して作業
   git checkout -b feature/login

2. 変更を commit して push
   git add .
   git commit -m "feat: ログイン機能を追加"
   git push -u origin feature/login

3. PR を作成
   gh pr create --title "ログイン機能を追加" --body "..."

4. レビュアーがコードを確認・コメント
   gh pr view
   gh pr diff

5. 修正が必要であれば追加 commit して push

6. 承認されたら main にマージ
   gh pr merge --squash
```

### 1-3. PR を使うメリット（個人開発でも）

個人開発であっても PR を使うことには意味があります。

- 過去の変更履歴が PR 単位で確認できる
- 何のために変更したかを PR の説明文に残せる
- 間違えたときに PR ごと Revert できる

---

## 2. gh pr create の全オプション

### 2-1. 基本的な使い方

```bash
# 対話形式で PR を作成（推奨）
gh pr create

# タイトルと本文を指定して作成
gh pr create --title "ログイン機能を追加" --body "## 変更内容\n- ログイン画面を追加"

# テンプレートを使って Web ブラウザで開く
gh pr create --web
```

### 2-2. 全オプション一覧

| オプション | 短縮形 | 説明 | 例 |
|-----------|--------|------|----|
| `--title` | `-t` | PR のタイトルを指定 | `--title "feat: ログイン追加"` |
| `--body` | `-b` | PR の本文を指定 | `--body "## 変更内容\n..."` |
| `--body-file` | `-F` | 本文をファイルから読み込む | `--body-file pr_body.md` |
| `--base` | `-B` | マージ先のブランチを指定（デフォルト: main） | `--base develop` |
| `--head` | `-H` | マージ元のブランチを指定（デフォルト: 現在のブランチ） | `--head feature/login` |
| `--draft` | `-d` | ドラフト PR として作成（レビュー未完了の場合） | `--draft` |
| `--reviewer` | `-r` | レビュアーを指定（GitHub ユーザー名） | `--reviewer alice,bob` |
| `--assignee` | `-a` | 担当者を指定 | `--assignee @me` |
| `--label` | `-l` | ラベルを付ける | `--label "bug,enhancement"` |
| `--milestone` | `-m` | マイルストーンを設定 | `--milestone "v1.0"` |
| `--project` | `-p` | GitHub Projects に追加 | `--project "My Project"` |
| `--template` | | 使用する PR テンプレートファイルを指定 | `--template bug_report.md` |
| `--fill` | | commit メッセージから自動でタイトル・本文を生成 | `--fill` |
| `--fill-first` | | 最初の commit メッセージからタイトルを生成 | `--fill-first` |
| `--fill-verbose` | | 全 commit メッセージを本文に展開 | `--fill-verbose` |
| `--web` | `-w` | ブラウザで PR 作成画面を開く | `--web` |
| `--no-maintainer-edit` | | メンテナーによるブランチ編集を禁止 | `--no-maintainer-edit` |

### 2-3. よく使うパターン

```bash
# ドラフト PR（まだレビュー不要・作業中）
gh pr create --draft --title "WIP: ログイン機能" --fill

# レビュアーとラベルを指定して作成
gh pr create \
  --title "feat: ログイン機能を追加" \
  --body-file .github/pr_body.md \
  --reviewer alice \
  --label "enhancement"

# commit メッセージから自動生成
gh pr create --fill

# 本文をファイルから読み込む
gh pr create --title "バグ修正" --body-file ./pr_description.md
```

> **ドラフト PR とは**
> まだレビューしてほしくない作業中の PR に使います。タイトルに "WIP:" を付ける慣習もありますが、`--draft` フラグを使うと GitHub 上で明示的にドラフト状態になります。準備ができたら `gh pr ready` で通常の PR に変換できます。

---

## 3. PR のレビュー

### 3-1. PR の内容を確認する

```bash
# PR の概要を表示（タイトル・本文・ステータス・レビュアー）
gh pr view

# 番号を指定して表示
gh pr view 42

# ブラウザで開く
gh pr view --web
```

`gh pr view` の出力例：

```
title:  feat: ログイン機能を追加
state:  OPEN
author: alice
labels: enhancement
assignees: alice
reviewers: bob

## 変更内容
- ログイン画面を追加
- JWT 認証を実装

Changes: 5 files changed, 120 insertions(+), 3 deletions(-)
```

### 3-2. 差分を確認する

```bash
# 変更差分を表示
gh pr diff

# 番号を指定
gh pr diff 42

# 特定ファイルの差分だけ表示
gh pr diff -- src/login.js
```

`gh pr diff` の出力例：

```diff
diff --git a/src/login.js b/src/login.js
new file mode 100644
index 0000000..1a2b3c4
--- /dev/null
+++ b/src/login.js
@@ -0,0 +1,30 @@
+function login(username, password) {
+  // ログイン処理
+}
```

### 3-3. コメントを投稿する

```bash
# PR にコメントを追加
gh pr comment 42 --body "LGTM! マージしてください"

# エディタを開いてコメントを書く
gh pr comment 42
```

### 3-4. レビュー（承認・変更要求）を行う

```bash
# PR を承認
gh pr review 42 --approve

# 変更を要求
gh pr review 42 --request-changes --body "この部分を修正してください"

# コメントのみ（承認でも却下でもない）
gh pr review 42 --comment --body "参考になります"
```

### 3-5. PR のチェックアウト（手元で動作確認）

```bash
# PR のブランチをローカルにチェックアウト
gh pr checkout 42

# チェックアウト後は通常のブランチとして操作できる
git log --oneline -5
```

---

## 4. gh pr merge の使い方

### 4-1. マージ方法の種類

PR をマージする方法は 3 種類あります。それぞれ Git の履歴（ツリー）が異なります。

#### merge（マージコミット）

```
main:    A --- B --------- M
                \         /
feature:         C --- D
```

feature ブランチのすべての commit が保持され、マージコミット（M）が作られます。

#### squash（スカッシュマージ）

```
main:    A --- B --- S
                      ↑
feature:  C --- D   （C と D をひとつにまとめた S）
```

feature ブランチの複数の commit を 1 つにまとめて main に追加します。feature ブランチ自体の履歴は main には残りません。

#### rebase（リベースマージ）

```
main:    A --- B --- C' --- D'
                      ↑
feature:  C --- D   （main の先頭に付け直した C', D'）
```

feature ブランチの commit を main の先頭に付け直して fast-forward します。マージコミットは作られません。

### 4-2. 3 種類の比較表

| 項目 | merge | squash | rebase |
|------|-------|--------|--------|
| マージコミット | 作られる | 作られない | 作られない |
| feature の commit 履歴 | main に残る | main に残らない（1つにまとめる） | main に残る（付け直し） |
| main の履歴の見やすさ | 分岐が残る | シンプル | 一直線 |
| 向いているケース | 大きな機能追加・履歴を残したい | 小さな修正・WIP commit が多い | 履歴を綺麗に保ちたい |
| conflict が起きやすいか | 低 | 低 | 高め |

### 4-3. gh pr merge のオプション

```bash
# マージコミットを作ってマージ
gh pr merge 42 --merge

# スカッシュマージ
gh pr merge 42 --squash

# リベースマージ
gh pr merge 42 --rebase

# マージ後にブランチを削除
gh pr merge 42 --squash --delete-branch

# マージコミットのタイトルを指定
gh pr merge 42 --merge --subject "Merge: ログイン機能を追加"

# 自動マージを有効化（CI が通ったら自動でマージ）
gh pr merge 42 --auto --squash
```

| オプション | 説明 |
|-----------|------|
| `--merge` | マージコミットを作成してマージ |
| `--squash` | commit をまとめてマージ |
| `--rebase` | リベースしてマージ |
| `--delete-branch` / `-d` | マージ後にリモートブランチを削除 |
| `--auto` | CI が全て通ったら自動でマージ |
| `--subject` | マージコミットのタイトルを指定 |
| `--body` | マージコミットの本文を指定 |
| `--admin` | ブランチ保護ルールを管理者権限で無視してマージ |

### 4-4. 推奨マージ方法

| 状況 | 推奨方法 | 理由 |
|------|----------|------|
| 個人開発・学習用リポジトリ | `--squash` | 履歴がシンプルになる |
| 小さな修正・バグ fix | `--squash` | WIP commit をまとめられる |
| 大きな機能（履歴を残したい） | `--merge` | レビューの文脈が残る |
| 履歴を一直線に保ちたい | `--rebase` | git log が見やすい |

---

## 5. PR のベストプラクティス

### 5-1. 小さい PR を作る

大きすぎる PR はレビューが困難になります。目安として 1 PR = 1 つの目的 にしましょう。

**悪い例（大きすぎる PR）:**
```
PR: 「ログイン・登録・パスワードリセット・メール通知を追加」
→ 変更ファイル数: 50
→ レビュアーが全体を把握できない
```

**良い例（小さい PR）:**
```
PR 1: 「ログイン画面を追加」（5ファイル）
PR 2: 「ユーザー登録画面を追加」（5ファイル）
PR 3: 「パスワードリセット機能を追加」（7ファイル）
```

| 観点 | 目安 |
|------|------|
| 変更行数 | 400行以内を目安にする |
| 変更ファイル数 | 10ファイル以内 |
| 目的 | 1 PR につき 1 つのこと |
| レビュー時間 | 15〜30分で完了できる量 |

### 5-2. わかりやすい説明文の書き方

PR の説明文（本文）には以下を含めると、レビュアーが判断しやすくなります。

```markdown
## 変更内容
- ログイン画面を追加
- JWT による認証を実装

## 変更理由
Issue #12 の要件に対応するため

## スクリーンショット（UI 変更がある場合）
（画像を貼り付け）

## 動作確認
- [ ] ログインできる
- [ ] 誤ったパスワードでエラーが出る
- [ ] ログアウトできる

## 関連 Issue
Closes #12
```

**タイトルのコツ:**
- Conventional Commits 形式を使う: `feat: ログイン機能を追加`
- 何をしたかが一目でわかる
- 長すぎない（72文字以内）

### 5-3. PR テンプレートの活用

リポジトリに PR テンプレートを置くと、PR 作成時に本文が自動的に埋まります。

**設置場所:**
```
.github/pull_request_template.md
```

**テンプレート例:**

```markdown
## 変更内容

<!-- 何を変更したか、箇条書きで記載 -->
-

## 変更理由

<!-- なぜこの変更が必要か -->

## 関連 Issue

<!-- 例: Closes #123 -->

## 動作確認

- [ ] ローカルで動作確認した
- [ ] テストが通っている
- [ ] ドキュメントを更新した（必要な場合）

## スクリーンショット

<!-- UI の変更がある場合は貼り付け -->

## レビュアーへのメモ

<!-- レビュアーに特に確認してほしいポイント -->
```

**テンプレートの設置手順:**

```bash
# .github ディレクトリを作成
mkdir -p .github

# テンプレートファイルを作成
touch .github/pull_request_template.md

# エディタで編集
code .github/pull_request_template.md

# commit して push
git add .github/pull_request_template.md
git commit -m "docs: PR テンプレートを追加"
git push
```

テンプレートを設置すると、次回から `gh pr create` で本文入力欄にテンプレートの内容が表示されます。

**複数テンプレートを使い分ける場合:**

```
.github/
└── PULL_REQUEST_TEMPLATE/
    ├── bug_fix.md
    ├── feature.md
    └── hotfix.md
```

```bash
# 特定テンプレートを指定
gh pr create --template bug_fix.md
```

---

## 6. gh CLI 便利コマンド一覧

### 6-1. PR 一覧・確認系

| コマンド | 説明 | 例 |
|---------|------|----|
| `gh pr list` | 開いている PR の一覧表示 | `gh pr list` |
| `gh pr list --state all` | 全ての PR を表示（closed 含む） | `gh pr list --state all` |
| `gh pr list --author @me` | 自分が作成した PR のみ表示 | `gh pr list --author @me` |
| `gh pr list --reviewer @me` | 自分がレビュアーの PR を表示 | `gh pr list --reviewer @me` |
| `gh pr list --label bug` | 特定ラベルの PR を表示 | `gh pr list --label enhancement` |
| `gh pr view` | 現在のブランチの PR を表示 | `gh pr view` |
| `gh pr view 42` | 番号で PR を表示 | `gh pr view 42` |
| `gh pr view --web` | ブラウザで PR を開く | `gh pr view --web` |
| `gh pr status` | 自分に関連する PR のステータスを一覧表示 | `gh pr status` |

### 6-2. PR 操作系

| コマンド | 説明 | 例 |
|---------|------|----|
| `gh pr create` | PR を作成 | `gh pr create --fill` |
| `gh pr edit 42` | PR のタイトル・本文・レビュアー等を編集 | `gh pr edit 42 --title "新タイトル"` |
| `gh pr ready 42` | ドラフト PR を通常の PR に変換 | `gh pr ready 42` |
| `gh pr close 42` | PR をクローズ（マージせずに閉じる） | `gh pr close 42` |
| `gh pr reopen 42` | クローズした PR を再オープン | `gh pr reopen 42` |

### 6-3. レビュー・チェックアウト系

| コマンド | 説明 | 例 |
|---------|------|----|
| `gh pr checkout 42` | PR のブランチをローカルにチェックアウト | `gh pr checkout 42` |
| `gh pr diff` | 現在の PR の差分を表示 | `gh pr diff` |
| `gh pr diff 42` | 番号指定で差分表示 | `gh pr diff 42` |
| `gh pr review 42 --approve` | PR を承認 | `gh pr review 42 --approve` |
| `gh pr review 42 --request-changes` | 変更を要求 | `gh pr review 42 --request-changes --body "修正お願いします"` |
| `gh pr comment 42` | PR にコメントを追加 | `gh pr comment 42 --body "LGTM"` |

### 6-4. gh pr status の見方

```bash
gh pr status
```

出力例：

```
Relevant pull requests in owner/repo

Current branch
  #45  feat: ログイン機能を追加 [feature/login]
  - Review: 1 approved, waiting for author
  - Checks: 3 passed

Created by you
  #43  fix: タイポを修正 [fix/typo] - Merged
  #44  docs: README を更新 [docs/readme] - Open

Requesting a code review from you
  #40  feat: 検索機能を追加 [feature/search] - Open
```

### 6-5. 実践的なワークフロー例

```bash
# 1. 作業ブランチを作成して変更を commit
git checkout -b feature/user-profile
# ... 作業 ...
git add src/profile.js
git commit -m "feat: ユーザープロフィール画面を追加"
git push -u origin feature/user-profile

# 2. PR を作成（テンプレートが自動的に使われる）
gh pr create \
  --title "feat: ユーザープロフィール画面を追加" \
  --reviewer alice \
  --label "enhancement"

# 3. 自分の PR のステータスを確認
gh pr status

# 4. レビューコメントを確認
gh pr view --comments

# 5. 修正が必要な場合は追加 commit して push
git add src/profile.js
git commit -m "fix: レビュー指摘を修正"
git push

# 6. 承認されたらマージ（ブランチも削除）
gh pr merge --squash --delete-branch
```

---

## まとめ

| ステップ | コマンド |
|---------|---------|
| PR 作成 | `gh pr create --title "..." --reviewer alice` |
| PR 確認 | `gh pr view` / `gh pr status` |
| 差分確認 | `gh pr diff` |
| ローカル確認 | `gh pr checkout 42` |
| 承認 | `gh pr review 42 --approve` |
| マージ | `gh pr merge --squash --delete-branch` |

Pull Request は、コードの品質を保ちながらチームで安全に開発するための重要な仕組みです。個人開発でも習慣として使うことで、将来チーム開発に参加したときにスムーズに対応できます。

---

本編はここまでです。お疲れ様でした！

さらに学びたい場合は補足資料へ：

- [supplement/04_conflict_resolution.md](./supplement/04_conflict_resolution.md) — マージとコンフリクト解決
- [supplement/05_team_workflow.md](./supplement/05_team_workflow.md) — チーム開発ワークフロー
- [supplement/06_command_reference.md](./supplement/06_command_reference.md) — コマンドリファレンス早見表

→ [補足資料一覧](./10_supplement_index.md)
