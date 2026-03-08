# 07. ブランチ戦略

複数人で同じリポジトリを触るとき、「誰がどのファイルを編集しているか」を管理しないと衝突が起きます。
このドキュメントでは、チームで使える最小限のブランチ戦略を学びます。

---

## 目次

1. [なぜブランチ戦略が必要か](#1-なぜブランチ戦略が必要か)
2. [GitHub Flow（シンプル・推奨）](#2-github-flow)
3. [ブランチ命名規則](#3-ブランチ命名規則)
4. [main ブランチを守るルール（Branch Protection Rules）](#4-main-ブランチを守るルール)
5. [実践：GitHub Flow で調査メモを公開する](#5-実践github-flow-で調査メモを公開する)

---

## 1. なぜブランチ戦略が必要か

### 複数人が同時に作業すると何が起きるか

たとえば 3 人がリポジトリに参加し、それぞれが `main` ブランチに直接 push したとします。

```
（ある日の午後）

Aさん  ──push──▶  main  ← 自分の変更を追加
Bさん  ──push──▶  main  ← Aさんの変更を上書きしてしまった！
Cさん  ──push──▶  main  ← Bさんが消した内容をさらに上書き…
```

こうなると：

- 誰かの変更が消える
- 「いつ・誰が・何を変えたか」が追いにくくなる
- バグが混入しても、どの変更が原因か分からない

### ブランチを使うとどう変わるか

```
          ┌── feature/aさん-調査メモ ──┐
          │                            │
main ─────┤                            ├──── main（安全な状態を維持）
          │                            │
          └── feature/bさん-手順書 ────┘
                    └── fix/誤字修正 ─ ┘
```

- 各自が独立したブランチで作業する
- `main` には完成・確認済みの内容だけをマージする
- コンフリクト（競合）が起きても、影響範囲が限定される

これを仕組みとして明文化したものが **ブランチ戦略** です。

---

## 2. GitHub Flow

GitHub Flow は GitHub 社が提案する、シンプルで学びやすいブランチ戦略です。
「`main` は常に安全な状態にする」という一つのルールを守るだけで運用できます。

### フロー全体図

```
1. main から作業ブランチを作る

   main
    │
    └──▶ feature/my-topic   ← ここで作業する


2. 作業ブランチで変更・commit を重ねる

   feature/my-topic
    ├── commit: "docs: 調査メモ追加"
    ├── commit: "fix: リンク修正"
    └── commit: "docs: 図を追加"


3. GitHub に push して Pull Request を開く

   feature/my-topic ──push──▶ GitHub
                                 │
                              Pull Request 作成
                              （レビュー・確認）


4. レビューが通ったら main にマージ

   feature/my-topic ──merge──▶ main


5. 作業ブランチを削除

   feature/my-topic を削除（main にマージ済みのため不要）
```

### 各ステップの詳細

#### ステップ 1: main から作業ブランチを作る

```bash
# まず main を最新の状態にする
git checkout main
git pull origin main

# 新しいブランチを作って移動
git checkout -b feature/調査メモ-クラウド入門
```

ブランチを切ることで、`main` を汚さずに自由に実験できます。

#### ステップ 2: 変更を commit する

```bash
# ファイルを編集したら
git add docs/cloud-intro.md
git commit -m "docs: クラウド入門の調査メモ追加"

# 続けて作業・commit を重ねてよい
git add docs/cloud-intro.md
git commit -m "docs: 参考リンクを追記"
```

こまめに commit すると、どの変更がどの目的か履歴で追えます。

#### ステップ 3: GitHub に push して PR を開く

```bash
# 初回 push（-u でリモートとブランチを紐付け）
git push -u origin feature/調査メモ-クラウド入門
```

push 後、GitHub の画面に「Compare & pull request」ボタンが表示されます。
クリックして Pull Request を作成します（詳細は [08_pull_request.md](./08_pull_request.md) で解説）。

#### ステップ 4: マージ

チームメンバーがレビューして問題なければ、GitHub の「Merge pull request」ボタンでマージします。

#### ステップ 5: ブランチ削除

マージ後は作業ブランチを削除してリポジトリをきれいに保ちます。

```bash
# リモートブランチ削除（GitHub Web UI のボタンでも可）
git push origin --delete feature/調査メモ-クラウド入門

# ローカルブランチ削除
git branch -d feature/調査メモ-クラウド入門
```

### main ブランチのルール

GitHub Flow の核心は、**`main` は常にデプロイ（公開・配信）できる状態にする** ことです。

| やること | やらないこと |
|---|---|
| PR 経由でマージする | main に直接 push する |
| マージ前にレビューする | 未完成のコードを main に入れる |
| CI が通ってからマージする | バグが含まれたままマージする |

---

## 3. ブランチ命名規則

ブランチ名はチーム全員が読むものです。「何のためのブランチか」が名前から分かると、作業の見通しが良くなります。

### 命名パターン

| プレフィックス | 用途 | 例 |
|---|---|---|
| `feature/` | 新機能・新コンテンツの追加 | `feature/調査メモ-kubernetes` |
| `fix/` | バグ修正・誤字修正 | `fix/リンク切れ-setup-md` |
| `docs/` | ドキュメントのみの更新 | `docs/readme-更新` |
| `refactor/` | 構成変更（内容変更なし） | `refactor/ファイル名-整理` |
| `chore/` | 設定ファイルや雑務 | `chore/gitignore-更新` |

### 命名のコツ

```
feature/[説明]
         └─── 何をするブランチか、1〜3 語で書く
```

**良い例**

```
feature/クラウド入門-調査メモ
fix/setup-手順-wsl2
docs/コマンド表-更新
```

**避けたい例**

```
test          ← 何のブランチか分からない
my-branch     ← 誰でも付けられる名前
temp          ← 永遠に "temp" のまま残りがち
aaa           ← 論外
```

### このリポジトリで使うルール

このリポジトリ（its_my_turn_lite）では以下を基本とします：

```
feature/  ─ 新しい調査メモ・コンテンツの追加
fix/      ─ 誤字・誤記・リンク切れの修正
docs/     ─ 既存ドキュメントの加筆・更新
```

---

## 4. main ブランチを守るルール

ルールを「口約束」にしておくと、誰かが誤って `main` に直接 push してしまうことがあります。
GitHub には **Branch Protection Rules（ブランチ保護ルール）** という機能があり、`main` への直接 push を技術的に禁止できます。

### Branch Protection Rules とは

| 機能 | 説明 |
|---|---|
| PR 必須 | `main` へのマージは PR 経由のみ許可 |
| レビュー承認必須 | 指定人数のレビュー承認がないとマージ不可 |
| ステータスチェック必須 | CI が通らないとマージ不可 |
| 直接 push の禁止 | ローカルから `git push` で直接 main に書けない |
| 強制 push の禁止 | `git push --force` を禁止 |

### GitHub Web UI での設定手順

以下の手順で `main` ブランチに保護ルールを設定します。

#### 手順 1: リポジトリの Settings を開く

```
GitHub リポジトリページ
  └── [Settings] タブをクリック
```

#### 手順 2: Branches メニューを開く

```
左サイドバー
  └── [Branches] をクリック
```

#### 手順 3: 保護ルールを追加する

```
Branch protection rules セクション
  └── [Add branch protection rule] ボタンをクリック
```

#### 手順 4: ルールを設定する

設定画面で以下を入力・選択します：

```
Branch name pattern: main
                     ↑ 保護するブランチ名を入力

チェックを入れる項目:
  [x] Require a pull request before merging
        └── Required number of approvals before merging: 1
              ↑ 最低 1 人のレビュー承認が必要

  [x] Do not allow bypassing the above settings
        ↑ 管理者も例外なくルールに従う
```

#### 手順 5: 保存する

```
ページ下部
  └── [Create] ボタンをクリック
```

#### 設定後の動作確認

設定後に `git push origin main` を試みると、次のようなエラーが出るようになります：

```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Changes must be made through a pull request.
To github.com:your-org/your-repo.git
 ! [remote rejected] main -> main (protected branch hook declined)
error: failed to push some refs to 'github.com:your-org/your-repo.git'
```

これが正常な動作です。`main` への変更は必ず PR を通す必要があります。

### 個人リポジトリでの注意点

個人リポジトリ（自分 1 人しかいない場合）では、レビュー承認者がいないため「Required number of approvals」を設定すると自分でマージできなくなります。

| 状況 | 推奨設定 |
|---|---|
| 1 人で開発 | 「Require a pull request」のみ有効、承認人数は 0 |
| 2 人以上 | 承認人数 1 以上を推奨 |
| 練習・学習用 | 設定なし（まずはフローを体で覚える） |

このドキュメントを読んでいる段階では、まずフローを体験することが目的なので、Branch Protection Rules は任意です。

---

## 5. 実践：GitHub Flow で調査メモを公開する

ここまで学んだ知識を組み合わせて、実際に調査メモを GitHub に公開してみましょう。

### シナリオ

「Markdown について調べたので、調査メモをリポジトリに追加する」

### ステップ 0: 準備（main を最新にする）

```bash
# main ブランチに移動
git checkout main

# リモートの最新状態を取得
git pull origin main
```

実行結果の例：

```
Already up to date.
```

または：

```
Updating a1b2c3d..e4f5g6h
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

### ステップ 1: 作業ブランチを作る

```bash
git checkout -b feature/markdown-調査メモ
```

実行結果：

```
Switched to a new branch 'feature/markdown-調査メモ'
```

ブランチが切れたか確認：

```bash
git branch
```

```
* feature/markdown-調査メモ
  main
```

`*` がついているブランチが現在の作業ブランチです。

### ステップ 2: ファイルを作成・編集する

`docs/markdown-memo.md` というファイルを新規作成します（VS Code でもターミナルでも OK）。

内容の例：

```markdown
# Markdown 調査メモ

## 概要

Markdown は、プレーンテキストに記法を加えることで
HTML に変換できる軽量マークアップ言語。

## 主な記法

| 記法 | 出力 |
|---|---|
| `# 見出し` | h1 タグ |
| `**太字**` | 太字テキスト |
| `[リンク](URL)` | リンク |

## 参考リンク

- [Markdown Guide](https://www.markdownguide.org/)
```

### ステップ 3: 変更をステージして commit する

```bash
# 変更ファイルを確認
git status
```

```
On branch feature/markdown-調査メモ
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        docs/markdown-memo.md
```

```bash
# ステージ
git add docs/markdown-memo.md

# commit（Conventional Commits 形式）
git commit -m "docs: Markdown 調査メモを追加"
```

```
[feature/markdown-調査メモ a1b2c3d] docs: Markdown 調査メモを追加
 1 file changed, 20 insertions(+)
 create mode 100644 docs/markdown-memo.md
```

### ステップ 4: GitHub に push する

```bash
git push -u origin feature/markdown-調査メモ
```

```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 520 bytes | 520.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'feature/markdown-調査メモ' on GitHub by visiting:
remote:      https://github.com/your-org/your-repo/pull/new/feature/markdown-調査メモ
remote:
To github.com:your-org/your-repo.git
 * [new branch]      feature/markdown-調査メモ -> feature/markdown-調査メモ
Branch 'feature/markdown-調査メモ' set up to track remote branch 'feature/markdown-調査メモ' from 'origin'.
```

### ステップ 5: Pull Request を作成する

GitHub のリポジトリページを開くと、「Compare & pull request」ボタンが表示されています。

```
GitHub リポジトリページ
  ┌─────────────────────────────────────────────────────────────┐
  │  feature/markdown-調査メモ had recent pushes 1 minute ago   │
  │                          [Compare & pull request]            │
  └─────────────────────────────────────────────────────────────┘
```

ボタンをクリックして PR を作成します（詳細は次のドキュメントで解説）。

### ステップ 6: マージ後のブランチ削除

PR がマージされたら、作業ブランチを削除します。

```bash
# main を最新にする
git checkout main
git pull origin main

# ローカルの作業ブランチを削除
git branch -d feature/markdown-調査メモ
```

```
Deleted branch feature/markdown-調査メモ (was a1b2c3d).
```

### フロー全体のまとめ

```
git checkout main
git pull origin main
        │
        ▼
git checkout -b feature/[ブランチ名]
        │
        ▼
ファイルを編集・作成
        │
        ▼
git add [ファイル]
git commit -m "[メッセージ]"
        │（必要に応じて繰り返す）
        ▼
git push -u origin feature/[ブランチ名]
        │
        ▼
GitHub で Pull Request を作成
        │
        ▼
レビュー → マージ
        │
        ▼
git checkout main
git pull origin main
git branch -d feature/[ブランチ名]
```

---

## まとめ

| 概念 | ポイント |
|---|---|
| ブランチ戦略 | チームで同時作業するための約束事 |
| GitHub Flow | main を安全に保ちながら開発する最小ルール |
| ブランチ命名規則 | `feature/` `fix/` `docs/` でブランチの目的を明示する |
| Branch Protection Rules | main への直接 push を技術的に禁止する |
| Pull Request | ブランチの変更を main にマージするための手続き |

---

## 次のステップ

[08_pull_request.md](./08_pull_request.md) — Pull Request ワークフロー
