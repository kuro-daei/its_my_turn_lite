# 06. Git の内部しくみ

---

## 目次

1. [なぜ内部しくみを知るのか](#1-なぜ内部しくみを知るのか)
2. [Git が管理する4つの領域](#2-git-が管理する4つの領域)
3. [.git ディレクトリの中身](#3-git-ディレクトリの中身)
4. [Git のオブジェクトモデル](#4-git-のオブジェクトモデル)
5. [コミットとは何か](#5-コミットとは何か)
6. [HEAD とは何か](#6-head-とは何か)
7. [ブランチの実体](#7-ブランチの実体)
8. [git status の3状態](#8-git-status-の3状態)
9. [git diff の3種類](#9-git-diff-の3種類)
10. [git log --oneline --graph --all の読み方](#10-git-log---oneline---graph---all-の読み方)
11. [まとめ](#11-まとめ)

---

## 1. なぜ内部しくみを知るのか

Git を使い始めると、こんな疑問にぶつかることがあります。

- `git add` したのに `git commit` しないと保存されないのはなぜ？
- ブランチを作っても容量がほとんど増えないのはなぜ？
- `detached HEAD` って何が起きているの？
- `git reset` と `git revert` の違いは？

これらの疑問はすべて、Git の内部しくみを知ることで「なるほど、だからそうなるのか」とクリアに理解できます。

### 内部しくみを学ぶメリット

| メリット | 具体例 |
|---|---|
| エラーの原因が分かる | `detached HEAD` の意味が分かり、慌てずに対処できる |
| コマンドの意味が分かる | `git add` が何をしているか分かる |
| トラブル解決が速くなる | `git reflog` でどこに戻ればよいか判断できる |
| 新しいコマンドを覚えやすい | 基本原理から類推できる |

Git は「賢いファイルシステム」とも呼ばれます。コンテンツをハッシュ値で管理するシンプルな原理の上に、ブランチ・マージ・履歴管理などの強力な機能が積み上がっています。

原理を知れば、Git は怖くなくなります。

---

## 2. Git が管理する4つの領域

Git はファイルを「4つの領域」に分けて管理します。この全体図を最初に頭に入れておくと、すべてのコマンドの意味がつかみやすくなります。

```
┌──────────────────────────────────────────────────────────────────────────┐
│                             あなたのPC の中                               │
│                                                                          │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────────────┐  │
│  │  ワーキングツリー  │    │  ステージ（Index）  │    │  ローカルリポジトリ    │  │
│  │                 │    │                 │    │   (.git ディレクトリ)  │  │
│  │  実際に編集する   │    │  コミット前の      │    │   コミット履歴が      │  │
│  │  ファイル群       │    │  スナップショット   │    │   永久保存される場所   │  │
│  │                 │    │                 │    │                      │  │
│  │  hello.py       │    │  hello.py (v2)  │    │  commit abc123       │  │
│  │  README.md      │    │                 │    │  commit def456       │  │
│  │  ...            │    │                 │    │  ...                 │  │
│  └────────┬────────┘    └────────┬────────┘    └──────────┬───────────┘  │
│           │                      │                         │              │
│           │   git add            │   git commit            │              │
│           │─────────────────────>│─────────────────────────>              │
│           │                      │                         │              │
│           │<─────────────────────│<────────────────────────               │
│           │   git restore        │   git restore --staged  │              │
│           │   git checkout       │   git reset HEAD        │              │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
                                        │  git push / git pull
                                        │
                               ┌────────▼──────────┐
                               │  リモートリポジトリ  │
                               │   (GitHub など)    │
                               │                   │
                               │  commit abc123     │
                               │  commit def456     │
                               │  ...               │
                               └───────────────────┘
```

### 各領域の役割

**ワーキングツリー（Working Tree）**
テキストエディタや VS Code で実際に見て編集するファイル群です。「作業ディレクトリ」とも呼ばれます。ここでの変更は、まだ Git に記録されていません。

**ステージ（Index / Staging Area）**
`git add` したファイルが置かれる一時的な場所です。「コミット予定のスナップショット」とイメージしてください。複数の変更があっても、ステージに上げたものだけをコミットできるため、変更を小さな単位に分けて記録できます。

**ローカルリポジトリ**
`.git` ディレクトリの中身そのものです。`git commit` するたびに、ステージのスナップショットが永久保存されます。コンテンツはハッシュ値（SHA-1）で管理されており、削除・改ざんが非常に困難な構造になっています。

**リモートリポジトリ**
GitHub などのサーバー上にあるリポジトリです。チームでの共有や、PC が壊れたときのバックアップとして機能します。`git push` でローカルの履歴をリモートへ送り、`git pull` でリモートの履歴をローカルへ取り込みます。

---

## 3. .git ディレクトリの中身

Git リポジトリを作ると、プロジェクトのルートに `.git` という隠しディレクトリが作られます。これが **ローカルリポジトリの本体** です。ここさえあれば、プロジェクトの全履歴が手元にあります。

### 実際に確認してみる

```bash
ls -la .git
```

```
total 52
drwxr-xr-x 8 user user 4096  3月  8 10:00 .
drwxr-xr-x 5 user user 4096  3月  8 10:00 ..
-rw-r--r-- 1 user user   23  3月  8 10:00 HEAD
-rw-r--r-- 1 user user  137  3月  8 10:00 config
-rw-r--r-- 1 user user   73  3月  8 10:00 description
drwxr-xr-x 2 user user 4096  3月  8 10:00 hooks/
-rw-r--r-- 1 user user  249  3月  8 10:05 index
drwxr-xr-x 2 user user 4096  3月  8 10:00 info/
drwxr-xr-x 3 user user 4096  3月  8 10:00 logs/
drwxr-xr-x 8 user user 4096  3月  8 10:05 objects/
drwxr-xr-x 5 user user 4096  3월  8 10:00 refs/
```

### 各ファイル・ディレクトリの役割

```
.git/
├── HEAD              ← 今どのブランチ（コミット）にいるか
├── config            ← このリポジトリ固有の設定
├── description       ← Gitリポジトリの説明文（gitweb用）
├── hooks/            ← コミット前後に自動実行するスクリプト
├── index             ← ステージの内容（バイナリ形式）
├── info/
│   └── exclude       ← .gitignore のリポジトリローカル版
├── logs/
│   ├── HEAD          ← HEAD の移動履歴（git reflog で使われる）
│   └── refs/         ← ブランチの移動履歴
├── objects/          ← すべてのオブジェクト（blob/tree/commit）
│   ├── ab/
│   │   └── cdef...   ← ハッシュの先頭2文字がディレクトリ名
│   ├── info/
│   └── pack/         ← 圧縮パックファイル
└── refs/
    ├── heads/        ← ローカルブランチ
    │   ├── main
    │   └── feature/xxx
    ├── remotes/      ← リモート追跡ブランチ
    │   └── origin/
    └── tags/         ← タグ
```

最重要ファイルは `HEAD`、`index`、`objects/`、`refs/` の4つです。これらを理解すれば、Git の動作が見えてきます。

### HEAD を見てみる

```bash
cat .git/HEAD
```

```
ref: refs/heads/main
```

`main` ブランチにいるとき、`HEAD` にはこのように書かれています。「HEAD は `refs/heads/main` を指している」という意味です。

### ブランチファイルを見てみる

```bash
cat .git/refs/heads/main
```

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

40文字の16進数——これが最新コミットの **ハッシュ値** です。ブランチの実体は、このコミットハッシュを書いたただのテキストファイルです。

---

## 4. Git のオブジェクトモデル

Git の核心は「**オブジェクトストア**」です。Git はすべてのデータを `objects/` ディレクトリに **オブジェクト** として保存します。

オブジェクトの種類は3つ（＋タグ用の1つ）です。

| オブジェクト | 役割 | 内容 |
|---|---|---|
| **blob** | ファイルの内容 | ファイルの生データ（ファイル名は持たない）|
| **tree** | ディレクトリの構造 | blob や tree への参照のリスト |
| **commit** | コミット情報 | tree・親コミット・作者・日時・メッセージ |

### オブジェクトの関係図

```
コミット (commit)
┌──────────────────────────────┐
│ tree: e3f1a2...              │ ──────────────────────────────┐
│ parent: a1b2c3...            │                               │
│ author: Alice <a@example.com>│                               ▼
│ date: 2026-03-08             │               ルートツリー (tree)
│ message: feat: add README    │               ┌─────────────────────────────┐
└──────────────────────────────┘               │ 100644 blob d4e5f6  README.md│
                                               │ 100644 blob 7a8b9c  hello.py │
                                               │ 040000 tree b1c2d3  src/      │
                                               └─────────────────────────────┘
                                                               │
                                                               ▼
                                                  サブツリー (tree: src/)
                                                  ┌───────────────────────────┐
                                                  │ 100644 blob f1e2d3  main.py│
                                                  └───────────────────────────┘
```

### ハッシュ値とは何か

Git はオブジェクトを保存するとき、その **内容** に対して SHA-1 ハッシュ関数を適用し、40文字の16進数を得ます。これがオブジェクトの「名前（ID）」になります。

```
「hello world\n」という内容
        ↓ SHA-1
8c7e5a3... （40文字のハッシュ値）
```

この仕組みには重要な性質があります。

- **同じ内容 → 必ず同じハッシュ**
  「hello.py の内容が同じなら blob は1つだけ」という重複排除が自動で起きます。

- **異なる内容 → 必ず異なるハッシュ**
  1文字でも変わればハッシュが変わるため、改ざんを即座に検知できます。

- **ハッシュから内容は復元できない**
  一方向関数なので、ハッシュを見ても元のファイルは分かりません。

### 実際にオブジェクトを確認する

コミットハッシュが分かれば、`git cat-file` コマンドで内容を見られます。

```bash
# コミットオブジェクトの型を確認
git cat-file -t a1b2c3d4e5f6

# コミットオブジェクトの内容を確認
git cat-file -p a1b2c3d4e5f6
```

```
tree e3f1a2b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0
parent 9f8e7d6c5b4a3f2e1d0c9b8a7f6e5d4c3b2a1f0
author Alice <alice@example.com> 1741392000 +0900
committer Alice <alice@example.com> 1741392000 +0900

feat: add README
```

```bash
# tree オブジェクトの内容を確認
git cat-file -p e3f1a2b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0
```

```
100644 blob d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9  README.md
100644 blob 7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d  hello.py
040000 tree b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6  src
```

```bash
# blob オブジェクトの内容を確認
git cat-file -p d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9
```

```
# My Project

このプロジェクトは...
```

---

## 5. コミットとは何か

### コミット = スナップショット + メタデータ

よく「コミットはファイルの差分を記録する」と思われがちですが、正確には違います。

**Git のコミットはその時点の全ファイルのスナップショット（tree オブジェクト経由）を記録します。**

差分ではなくスナップショットを持つことで、特定のコミットへの復元が非常に高速になります。（差分管理だと過去に遡るほど計算が必要になります）

### コミットチェーン

各コミットは「親コミット（parent）」への参照を持っています。これが **コミット履歴** を形成します。

```
                                 今のコミット
                                     │
                              ┌──────┘
                              │
   ┌─────────┐    ┌─────────┐ │ ┌─────────┐
   │ commit  │    │ commit  │ │ │ commit  │
   │ a1b2c3  │◄───│ d4e5f6  │◄┘ │ g7h8i9  │  ← 最新
   │         │    │         │◄──│         │
   │ tree:...|    │ tree:.. │   │ tree:.. │
   │ parent:0│    │ parent: │   │ parent: │
   │         │    │  a1b2c3 │   │  d4e5f6 │
   └─────────┘    └─────────┘   └─────────┘
   （初回コミット）

   ◄──────────────────────────────────────
                   過去                 現在
```

各コミットが親コミットのハッシュを保持することで、**チェーン（鎖）** が形成されます。

### コミットの不変性

コミットオブジェクトは一度作成されると **内容を変更できません**。なぜなら、内容が変わればハッシュ値が変わり、そのコミットを参照している親コミットも変えなければならなくなるからです。

`git commit --amend` をすると「コミットを修正した」と感じますが、実際には新しいコミットオブジェクトが作られています。元のコミットオブジェクトは `.git/objects/` に残ったままです。

この不変性が Git の信頼性の根拠です。

---

## 6. HEAD とは何か

`HEAD` は「今あなたがいる場所」を示すポインタです。`.git/HEAD` ファイルに書かれています。

### 通常の状態：HEAD → ブランチ → コミット

```
.git/HEAD の内容:  ref: refs/heads/main
                          │
                          ▼
.git/refs/heads/main の内容:  a1b2c3d4e5f6...
                                      │
                                      ▼
                              コミットオブジェクト a1b2c3...
                              ┌────────────────────┐
                              │ tree: e3f1a2...     │
                              │ parent: 9f8e7d...   │
                              │ message: feat: ...  │
                              └────────────────────┘
```

通常、HEAD は **ブランチ名** を経由してコミットを指します。新しいコミットをすると、ブランチが最新コミットを指すように自動更新され、HEAD は変わらずそのブランチを指し続けます。

```bash
# コミット後のブランチの動き
# 前: main → a1b2c3
git commit -m "feat: new feature"
# 後: main → g7h8i9（新コミット）、HEAD は変わらず main を指す
```

### detached HEAD 状態

`git checkout <コミットハッシュ>` など、ブランチではなく **コミットを直接指定** すると、HEAD がブランチ名ではなくコミットハッシュを直接指す状態になります。これを **detached HEAD（切り離された HEAD）** と呼びます。

```bash
git checkout a1b2c3d4e5f6
```

```
Warning: You are in 'detached HEAD' state. You can look around, make
experimental changes and commit them, and you can discard any commits
you make in this state without impacting any branches by switching back
to a branch.
```

このとき `.git/HEAD` の内容は次のようになります。

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

（ブランチ参照ではなく、ハッシュ値が直接書かれる）

```
通常:          HEAD → main → commit g7h8i9
                                         │
detached HEAD: HEAD ───────────────────> commit a1b2c3
               （ブランチを経由しない）
```

### detached HEAD の注意点

detached HEAD 状態でコミットすると、そのコミットはどのブランチにも属しません。別のブランチに移動した後、参照がなくなったコミットは **ガベージコレクション** で削除されます。

detached HEAD に気づいたら、次のどちらかを行います。

```bash
# 元のブランチに戻る（コミットを破棄）
git checkout main

# 新しいブランチを作って保存する
git checkout -b feature/experiment
```

---

## 7. ブランチの実体

ブランチの実体は非常にシンプルです。`.git/refs/heads/` ディレクトリに置かれた **コミットハッシュを1行だけ書いたテキストファイル** です。

### 実際に確認する

```bash
# ローカルブランチの一覧（実ファイル）
ls .git/refs/heads/
```

```
main
feature/add-login
fix/typo-readme
```

```bash
# ブランチの実体を見る
cat .git/refs/heads/main
```

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

```bash
cat .git/refs/heads/feature/add-login
```

```
d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3
```

### ブランチ作成のコストが低い理由

`git branch feature/new-feature` を実行すると、`.git/refs/heads/feature/new-feature` というファイルが作られ、現在のコミットハッシュが書き込まれます。それだけです。

```bash
# ブランチを作る = たった40バイトのファイルを1つ作るだけ
git branch feature/new-feature
cat .git/refs/heads/feature/new-feature
```

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

ファイルのコピーも差分の計算も不要です。だから Git のブランチ作成は一瞬で完了し、容量もほとんど消費しません。

### コミットするとブランチファイルが更新される

```
コミット前: .git/refs/heads/main = a1b2c3...

git commit → 新コミット g7h8i9... が作られる

コミット後: .git/refs/heads/main = g7h8i9...（自動更新）
```

ブランチとは「特定のコミットに貼られた付箋」です。コミットするたびに付箋が最新コミットへ移動していくイメージです。

### リモート追跡ブランチ

```bash
ls .git/refs/remotes/origin/
```

```
HEAD
main
feature/add-login
```

```bash
cat .git/refs/remotes/origin/main
```

```
f1e2d3c4b5a6f1e2d3c4b5a6f1e2d3c4b5a6f1e2
```

リモート追跡ブランチ（`origin/main` など）も同じ構造です。`git fetch` するとこのファイルが更新されます。

---

## 8. git status の3状態

ワーキングツリーのファイルは、Git から見ると常に次の3つの状態のどれかにあります。

```
┌─────────────────────────────────────────────────────────────────────┐
│                           ファイルの状態遷移                           │
│                                                                     │
│   ワーキングツリー                ステージ          ローカルリポジトリ   │
│                                                                     │
│                                                                     │
│   [Untracked]                                                       │
│   （新規作成・                  ┌──────────┐                          │
│     Git未管理）                 │          │                          │
│         │                      │  Staged  │                          │
│         │  git add ──────────► │          │                          │
│         │                      │（コミット  │   git commit ──────────► │
│                                │  待ち）   │                          │
│   [Modified]                   │          │                          │
│   （既存ファイルを               └──────────┘                          │
│     変更済み）                        │                               │
│         │                            │ git restore --staged           │
│         │  git add ──────────────────►（ステージから取り消し）           │
│         │                                                            │
│         │ git restore（変更を破棄）                                    │
│         ◄────────────────────────────────────────────────────────── │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 各状態の詳細

**Untracked（追跡されていない）**
Git がまだ管理していない新規ファイルです。`git add` するまでは何をしても Git に記録されません。

```bash
# 新しいファイルを作る
touch newfile.py
git status
```

```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        newfile.py

nothing added to commit but untracked files present
```

**Modified（変更済み）**
Git が管理しているファイルを変更したが、まだ `git add` していない状態です。

```bash
# 既存ファイルを変更する
echo "print('hello')" >> hello.py
git status
```

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.py
```

**Staged（ステージ済み）**
`git add` した後の状態です。次の `git commit` でリポジトリに記録されます。

```bash
git add hello.py
git status
```

```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.py
```

### 状態を戻すコマンド対応表

| 操作 | コマンド |
|---|---|
| ステージ → Modified に戻す | `git restore --staged <file>` |
| Modified → 最後のコミット状態に戻す | `git restore <file>` |
| Staged を含めて最後のコミット状態に戻す | `git restore --staged <file>` → `git restore <file>` |

> **注意：** `git restore <file>` は **ワーキングツリーの変更を完全に破棄** します。元に戻せないため、慎重に実行してください。

---

## 9. git diff の3種類

`git diff` コマンドには「どの領域を比較するか」によって3パターンあります。これを混同するとレビュー内容がずれるので、しっかり区別しましょう。

### 全体像

```
ワーキングツリー  ─── git diff ───────────────────► ステージ（Index）
       │                                                  │
       │                                                  │
       │                                   git diff --staged
       │                                                  │
       │                                                  ▼
       │                                         ローカルリポジトリ
       │                                          （HEAD コミット）
       │                                                  │
       └────────────── git diff HEAD ─────────────────────►
```

### 1. `git diff` — ワーキングツリー vs ステージ

ステージに上げていない変更（まだ `git add` していない部分）を表示します。

```bash
git diff
```

```diff
diff --git a/hello.py b/hello.py
index 7a8b9c0..d4e5f6a 100644
--- a/hello.py
+++ b/hello.py
@@ -1,3 +1,5 @@
 def greet(name):
-    print(f"Hello, {name}!")
+    message = f"Hello, {name}!"
+    print(message)
+    return message
```

**使い所：** `git add` の前に「何を変えたか」を確認するとき。

### 2. `git diff --staged` — ステージ vs 最新コミット（HEAD）

`git add` 済みで、次のコミットに含まれる変更を表示します。

```bash
git diff --staged
# または（どちらも同じ）
git diff --cached
```

**使い所：** `git commit` の前に「今コミットすると何が記録されるか」を確認するとき。コードレビューの自己チェックにも有効です。

### 3. `git diff HEAD` — ワーキングツリー vs 最新コミット（HEAD）

ステージ済みの変更も未ステージの変更も含めた、**最後のコミットからの全変更** を表示します。

```bash
git diff HEAD
```

**使い所：** 「前回コミットから今まで何を変えたか」をまとめて確認するとき。

### コミット間の比較

```bash
# 特定の2コミット間の差分
git diff a1b2c3..d4e5f6

# ブランチ間の差分
git diff main..feature/add-login

# ブランチが分岐してからの差分（よく使う）
git diff main...feature/add-login
```

---

## 10. git log --oneline --graph --all の読み方

`git log` はコミット履歴を表示するコマンドです。オプションを組み合わせると、ブランチの分岐・マージの全体像が見えます。

### 基本的な使い方

```bash
git log --oneline --graph --all
```

```
* a1b2c3d (HEAD -> main, origin/main) feat: add login feature
*   9f8e7d6 Merge branch 'feature/add-button'
|\
| * 7a8b9c0 feat: add submit button
| * d4e5f6a feat: add form component
* | f1e2d3c fix: correct typo in README
|/
* e3f4a5b feat: initial commit
```

### 記号の読み方

| 記号 | 意味 |
|---|---|
| `*` | 1つのコミット |
| `\|` | ブランチのライン |
| `\|` と `*` の組み合わせ | 並行して進んでいたブランチ |
| `\|` ← `\|` の結合 | マージされた地点 |
| `(HEAD -> main)` | 現在位置 → ブランチ名 |
| `(origin/main)` | リモート追跡ブランチの位置 |

### 実際に読む

```
* a1b2c3d (HEAD -> main, origin/main) feat: add login feature
```
→ 最新コミット。`main` ブランチはここを指している。リモートも同じ位置。

```
*   9f8e7d6 Merge branch 'feature/add-button'
|\
```
→ マージコミット。`|` が2本に広がっているので、ここで2本のブランチが合流した。

```
| * 7a8b9c0 feat: add submit button
| * d4e5f6a feat: add form component
```
→ `feature/add-button` ブランチで行われたコミット群。

```
* | f1e2d3c fix: correct typo in README
```
→ `feature/add-button` ブランチが動いていた間、`main` でも別のコミットが行われた。

```
|/
* e3f4a5b feat: initial commit
```
→ 2本のラインが1本に合流（分岐した元のコミット）。

### よく使うオプション

```bash
# 作者とコミット日時も表示
git log --oneline --graph --all --format="%h %an %ar %s"

# 直近10件だけ
git log --oneline -10

# 特定のファイルの変更履歴
git log --oneline -- hello.py

# 特定のキーワードを含むコミット
git log --oneline --grep="feat"
```

---

## 11. まとめ

この章で学んだ全体像を1枚の図にまとめます。

### 全体の状態遷移図

```
                         【ファイルの状態と操作の全体図】

                 ワーキングツリー          ステージ(Index)       ローカルリポジトリ
                                                                  (objects/)
 新規ファイル
 作成後           [Untracked]
                      │
                      │ git add
                      │
                      ▼
                  [Staged]  ─────────────────── git commit ──────────────► [commit]
                      │                                                         │
                      │ git restore --staged                                    │
                      ▼                                                         │
 既存ファイル        [Modified]                                                   │
 変更後                │                                                         │
                      │ git add                                                 │
                      │                                                         │
                      ▼                                                         │
                  [Staged]  ─────────────────── git commit ──────────────► [commit]
                      │                                                         │
                      │ git restore --staged                                    │
                      ▼                                                         │
                  [Modified]                                                    │
                      │                                                         │
                      │ git restore（変更破棄）                                  │
                      │◄────────────────────────────────────────────────────── │
                                                                                │
                                                                         git push
                                                                                │
                                                                                ▼
                                                                          リモートリポジトリ
```

### HEAD とブランチとコミットの関係まとめ

```
HEAD
 │
 └──► refs/heads/main （ブランチファイル: .git/refs/heads/main）
              │
              └──► commit g7h8i9 ─► commit d4e5f6 ─► commit a1b2c3 ─► ...
                       │                │                │
                       ▼                ▼                ▼
                     tree             tree             tree
                    /  |  \          /  |  \          /  |  \
                  blob blob tree  blob blob tree  blob blob tree
                       ...             ...             ...
```

### この章のキーポイント

| キーワード | 一言まとめ |
|---|---|
| blob | ファイル内容のスナップショット（ファイル名なし） |
| tree | ディレクトリ構造（blob と tree への参照リスト） |
| commit | tree + 親コミット + メタデータ。不変オブジェクト |
| HEAD | 「今いる場所」を示すポインタ。通常はブランチを経由する |
| ブランチ | コミットハッシュを書いた40バイトのテキストファイル |
| ステージ | コミット前の準備エリア。`.git/index` に保存 |
| Untracked | 新規作成で Git が管理していないファイル |
| Modified | 変更済みだが git add していないファイル |
| Staged | git add 済みで次のコミット待ちのファイル |
| detached HEAD | ブランチ経由せずコミットを直接指している状態 |

### 次に学ぶこと

Git の内部しくみを理解した今、以下のコマンドがより深く使えるようになります。

- `git reflog` — HEAD の移動履歴を見る（「消えた」コミットを探せる）
- `git stash` — 作業中の変更を一時退避する
- `git rebase` — コミット履歴を整理する（コミットの付け替え）
- `git bisect` — バグの原因コミットを二分探索で見つける

---

[07_markdown.md](./07_markdown.md) — Markdown 記法
