# 06. Git の基本概念

---

## 目次

1. [Git が管理する4つの領域](#1-git-が管理する4つの領域)
2. [コミットとは何か](#2-コミットとは何か)
3. [HEAD とは何か](#3-head-とは何か)
4. [git status の3状態](#4-git-status-の3状態)
5. [git diff の3種類](#5-git-diff-の3種類)
6. [git log の読み方](#6-git-log-の読み方)
7. [まとめ](#7-まとめ)

---

## 1. Git が管理する4つの領域

Git はファイルを「4つの領域」に分けて管理します。この全体図を頭に入れておくと、コマンドの意味がつかみやすくなります。

```
┌──────────────────────────────────────────────────────────┐
│                      あなたの PC                          │
│                                                          │
│  ワーキングツリー  ─git add─▶  ステージ  ─git commit─▶  ローカルRepo  │
│  (編集中のファイル)            (コミット予告)  (確定した履歴)           │
│                                                          │
└──────────────────────────────────────────────────────────┘
                                    │ git push / git pull
                                    ▼
                           ┌─────────────────┐
                           │  リモートRepo     │
                           │   (GitHub)       │
                           └─────────────────┘
```

### 各領域の役割

| 領域 | 役割 |
|---|---|
| ワーキングツリー | VS Code などで実際に編集するファイル群 |
| ステージ（Index） | `git add` したファイルの一時置き場。コミットに含む内容を選ぶ |
| ローカルリポジトリ | `git commit` で確定した履歴が積み重なる場所 |
| リモートリポジトリ | GitHub 上にある共有リポジトリ。`push` / `pull` で同期 |

---

## 2. コミットとは何か

コミットは「その時点の全ファイルのスナップショット」を記録したものです。差分ではなく丸ごと保存するため、過去の状態への復元が一瞬でできます。

```
コミット履歴のイメージ:

  ●─────●─────●─────●  ← main（最新コミットを指す）
  A     B     C     D
  │     │     │     │
 古い  ←  時間の流れ  →  新しい
```

各コミットは前のコミットをつないでいます。これによって「どの順番で何を変えたか」という履歴が作られます。

---

## 3. HEAD とは何か

`HEAD` は「今自分がいるコミット（ブランチ）」を示すポインタです。

```
通常の状態:

  HEAD → main → 最新コミット
```

`git switch feature/xxx` でブランチを切り替えると、HEAD が移動します。

```
ブランチ切り替え後:

  HEAD → feature/xxx → そのブランチの最新コミット
```

コミットするたびに、HEAD が指すブランチが自動的に最新コミットを指すように更新されます。

---

## 4. git status の3状態

`git status` でファイルがどの状態にあるか確認できます。

```
Untracked  ─git add─▶  Staged  ─git commit─▶  リポジトリへ
（新規・未管理）             （コミット待ち）

Modified   ─git add─▶  Staged
（変更済み・未add）
```

| 状態 | 意味 | `git status` の表示 |
|---|---|---|
| Untracked | 新規作成で Git が管理していない | `Untracked files:` |
| Modified | 既存ファイルを変更済み・未 add | `Changes not staged for commit:` |
| Staged | `git add` 済み・次のコミット待ち | `Changes to be committed:` |

### 状態を戻すコマンド

| 操作 | コマンド |
|---|---|
| Staged → Modified に戻す | `git restore --staged <file>` |
| Modified → 最後のコミット状態に戻す | `git restore <file>` |

> **注意：** `git restore <file>` は変更を完全に破棄します。元に戻せないため慎重に。

---

## 5. git diff の3種類

`git diff` は「どの領域を比較するか」によって3パターンあります。

```
ワーキングツリー ─── git diff ──────────────▶ ステージ
      │                                          │
      │                           git diff --staged
      │                                          ▼
      │                                  ローカルリポジトリ（HEAD）
      │                                          │
      └──────────── git diff HEAD ───────────────►
```

| コマンド | 何を比較するか | 使いどころ |
|---|---|---|
| `git diff` | ワーキングツリー vs ステージ | `git add` の前に確認 |
| `git diff --staged` | ステージ vs 最新コミット | `git commit` の前に確認 |
| `git diff HEAD` | ワーキングツリー vs 最新コミット | 前回コミットから全変更を確認 |

### ブランチ間の比較

```bash
# main と feature ブランチの差分
git diff main..feature/add-login

# ブランチが分岐してからの差分
git diff main...feature/add-login
```

---

## 6. git log の読み方

`git log` でコミット履歴を確認できます。

```bash
git log --oneline --graph --all
```

```
* a1b2c3d (HEAD -> main, origin/main) feat: ログイン機能を追加
*   9f8e7d6 Merge branch 'feature/add-button'
|\
| * 7a8b9c0 feat: ボタンを追加
| * d4e5f6a feat: フォームを追加
* | f1e2d3c fix: README のタイポを修正
|/
* e3f4a5b feat: 最初のコミット
```

| 記号 | 意味 |
|---|---|
| `*` | 1つのコミット |
| `(HEAD -> main)` | 今いる場所 → ブランチ名 |
| `(origin/main)` | GitHub 上のブランチの位置 |
| `|\` と `|/` | ブランチが分岐・合流した地点 |

### よく使うオプション

```bash
# 直近 10 件だけ
git log --oneline -10

# 特定のファイルの変更履歴
git log --oneline -- README.md

# 特定のキーワードを含むコミット
git log --oneline --grep="feat"
```

---

## 7. まとめ

```
ファイルを編集
    │
    │ git add <file>
    ▼
ステージに追加（コミット予告）
    │
    │ git commit -m "メッセージ"
    ▼
ローカルリポジトリに記録
    │
    │ git push
    ▼
GitHub（リモート）に反映
```

| コマンド | 役割 |
|---|---|
| `git status` | ファイルの状態を確認 |
| `git diff` | add 前の変更内容を確認 |
| `git diff --staged` | commit 前の変更内容を確認 |
| `git log --oneline` | コミット履歴を確認 |

---

## 補足資料

Git の内部しくみをさらに深く学びたい場合：

- [supplement/03_git_deep_dive.md](./supplement/03_git_deep_dive.md) — .git 構造・オブジェクトモデル・detached HEAD・発展コマンド

---

[07_markdown.md](./07_markdown.md) — Markdown 記法
