# 補足 03. Git の内部しくみ（詳細）

> **位置づけ:** この補足資料は [06_git_internals.md](../06_git_internals.md) の発展内容です。
> Git の基本操作（add/commit/push）に慣れてから読むことを推奨します。

---

## なぜ内部しくみを知るのか

| メリット | 具体例 |
|---|---|
| エラーの原因が分かる | `detached HEAD` の意味が分かり、慌てずに対処できる |
| コマンドの意味が分かる | `git add` が何をしているか分かる |
| トラブル解決が速くなる | `git reflog` でどこに戻ればよいか判断できる |
| 新しいコマンドを覚えやすい | 基本原理から類推できる |

---

## .git ディレクトリの中身

Git リポジトリを作ると、プロジェクトのルートに `.git` という隠しディレクトリが作られます。これが **ローカルリポジトリの本体** です。

### 実際に確認してみる

```bash
ls -la .git/
```

```
drwxr-xr-x  HEAD
drwxr-xr-x  config
drwxr-xr-x  description
drwxr-xr-x  hooks/
drwxr-xr-x  index
drwxr-xr-x  info/
drwxr-xr-x  objects/
drwxr-xr-x  refs/
```

### 主なファイル・ディレクトリの役割

| パス | 役割 |
|---|---|
| `HEAD` | 現在のブランチ（またはコミット）を指すポインタ |
| `config` | リポジトリの設定（リモート URL など） |
| `objects/` | コミット・ファイル内容を格納するオブジェクトストア |
| `refs/heads/` | 各ブランチが指すコミットハッシュ |
| `refs/remotes/` | リモート追跡ブランチ |
| `index` | ステージングエリアの内容 |

### HEAD を見てみる

```bash
cat .git/HEAD
```

```
ref: refs/heads/main
```

ブランチ名が書かれています。ここを読んで Git は「今どのブランチにいるか」を知ります。

### ブランチファイルを見てみる

```bash
cat .git/refs/heads/main
```

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

ブランチの実体は「コミットハッシュを1行だけ書いたテキストファイル」です。`git branch feature/xxx` を実行すると、このファイルが1つ増えるだけです。ファイルコピーも差分計算も不要なため、ブランチ作成は一瞬で完了します。

---

## Git のオブジェクトモデル

Git は内部で4種類のオブジェクトを使ってすべてのデータを管理しています。

| オブジェクト | 役割 |
|---|---|
| blob | ファイルの内容そのもの（ファイル名なし） |
| tree | ディレクトリ構造（blob と tree への参照リスト） |
| commit | tree + 親コミット + 作者・日時・メッセージ |
| tag | 特定のコミットへの名前付き参照 |

### オブジェクトの関係図

```
commit g7h8i9
    │
    ├── parent: d4e5f6  （前のコミットへの参照）
    ├── tree: e3f1a2    （その時点のディレクトリ構造）
    │       │
    │       ├── blob a1b2c3  README.md の内容
    │       ├── blob b2c3d4  hello.py の内容
    │       └── tree c3d4e5  src/ ディレクトリ
    │               │
    │               └── blob d4e5f6  src/main.py の内容
    └── message: "feat: ..."
```

### ハッシュ値とは何か

各オブジェクトは SHA-1 ハッシュ（40文字の16進数）で識別されます。

```bash
# 任意のオブジェクトの内容を確認
git cat-file -p a1b2c3d
```

ハッシュ値は内容から計算されます。内容が1バイトでも変われば別のハッシュになります。これにより Git は改ざんを検出できます。

---

## コミットの詳細

### コミットチェーン

各コミットは「親コミット」への参照を持っています。これがコミット履歴を形成します。

```
   ┌─────────┐    ┌─────────┐    ┌─────────┐
   │ commit  │    │ commit  │    │ commit  │
   │ a1b2c3  │◄───│ d4e5f6  │◄───│ g7h8i9  │  ← 最新
   │ parent:0│    │ parent: │    │ parent: │
   │         │    │  a1b2c3 │    │  d4e5f6 │
   └─────────┘    └─────────┘    └─────────┘
  （初回コミット）
```

### コミットの不変性

コミットオブジェクトは一度作成されると内容を変更できません。`git commit --amend` は「修正」ではなく、新しいコミットを作り直しています。元のコミットは `.git/objects/` に残ったままです。

---

## HEAD の詳細

### detached HEAD 状態

`git checkout <コミットハッシュ>` でブランチではなくコミットを直接指定すると、HEAD がブランチ名ではなくコミットハッシュを直接指す状態になります。これを **detached HEAD** と呼びます。

```bash
git checkout a1b2c3d4e5f6
```

```
Warning: You are in 'detached HEAD' state. ...
```

```
通常:          HEAD → main → commit g7h8i9
detached HEAD: HEAD ──────────────────► commit a1b2c3
               （ブランチを経由しない）
```

### detached HEAD の注意点

detached HEAD 状態でコミットすると、そのコミットはどのブランチにも属しません。別のブランチに移動すると参照がなくなり、後で見つけにくくなります。

```bash
# 元のブランチに戻る（コミットを破棄）
git switch main

# 新しいブランチを作って保存する
git switch -c feature/experiment
```

---

## リモート追跡ブランチ

`origin/main` のような `origin/` が付いたブランチは「リモート追跡ブランチ」です。

```bash
git branch -a
```

```
* main
  feature/add-login
  remotes/origin/main      ← リモート追跡ブランチ
  remotes/origin/feature/add-login
```

`git fetch` を実行するとリモートの情報が更新されます（ローカルのブランチは変更されません）。`git pull` は `git fetch` + `git merge` の組み合わせです。

---

## 発展コマンド

```bash
# HEAD の移動履歴を見る（「消えた」コミットを探せる）
git reflog

# 作業中の変更を一時退避する
git stash
git stash pop

# コミット履歴を整理する
git rebase -i HEAD~3

# バグの原因コミットを二分探索で見つける
git bisect start
git bisect bad
git bisect good <commit>
```

---

[補足資料一覧に戻る](../91_supplement_index.md)
