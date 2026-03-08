# 10. 複数人協働シナリオ演習

このドキュメントでは、1つのリポジトリを複数人で共有する方法を学びます。
Collaborator の追加から、2人が同時に作業するシナリオ、コンフリクトの解決、そして `git pull` と `git fetch` の使い分けまでを順番に解説します。

---

## 目次

1. [チーム開発のイメージ](#1-チーム開発のイメージ)
2. [Collaborator の追加手順](#2-collaborator-の追加手順)
3. [シナリオ演習：2人が同時に作業する](#3-シナリオ演習2人が同時に作業する)
4. [git pull vs git fetch の使い分け](#4-git-pull-vs-git-fetch-の使い分け)
5. [リモート追跡ブランチ](#5-リモート追跡ブランチ)

---

## 1. チーム開発のイメージ

複数人で1つのリポジトリを共有するとき、全員が同じリモートリポジトリ（GitHub 上）を中心に作業します。

```
          GitHub（リモートリポジトリ）
          ┌─────────────────────────┐
          │   kuro-daei/my-repo     │
          │   main ブランチ          │
          └────────┬────────────────┘
                   │
       ┌───────────┼───────────┐
       │           │           │
       ▼           ▼           ▼
  Aさんの PC   Bさんの PC   Cさんの PC
  (ローカル)   (ローカル)   (ローカル)
```

- 全員が `git clone` で同じリポジトリをコピーして作業する
- 変更は `git push` でリモートに送り、`git pull` でリモートの変更を取り込む
- 誰かが push したら、他の人は pull して最新の状態に追いつく

### ブランチを使った協働フロー

1人ずつ **ブランチを切って** 作業し、完成したら **Pull Request** を出してレビュー後にマージします。

```
main ──────────●──────────────────●──────── ...
               │                  ▲
               │ (clone/pull)     │ (merge)
               ▼                  │
Aさん:   feature/a ────────────── PR
               │
               │ (clone/pull)
               ▼
Bさん:         feature/b ──────── PR ──▶ merge
```

---

## 2. Collaborator の追加手順

GitHub のリポジトリに別のアカウントを招待することで、そのアカウントも `git push` できるようになります。

### 手順

**1. リポジトリの Settings を開く**

GitHub でリポジトリのページを開き、上部の「Settings」タブをクリックします。

```
[Code] [Issues] [Pull requests] [Actions] [Settings]  ← ここ
```

**2. Collaborators を選ぶ**

左のサイドバーから「Collaborators」をクリックします。
（「Access」セクションにある場合もあります）

```
Settings
└── Access
    └── Collaborators   ← ここ
```

**3. 「Add people」ボタンをクリック**

「Manage access」画面が表示されるので、「Add people」ボタンをクリックします。

**4. 招待したいユーザーを検索して選択**

GitHub のユーザー名またはメールアドレスを入力して、対象のアカウントを選択します。

**5. 「Add [ユーザー名] to this repository」をクリック**

招待メールが相手に送られます。

**6. 相手が招待を承認する**

招待されたユーザーは GitHub からメールを受け取り、「Accept invitation」ボタンをクリックすることで Collaborator になります。

承認後は、相手も `git push` や `git pull` が自由にできるようになります。

### Collaborator の権限

| 操作 | Collaborator |
|------|-------------|
| コードの閲覧 | できる |
| Issue / PR の作成 | できる |
| `git push` | できる |
| Settings の変更 | できない |
| リポジトリの削除 | できない |

---

## 3. シナリオ演習：2人が同時に作業する

ここでは **Aさん** と **Bさん** が同じリポジトリで協働するシナリオを体験します。

### 前提

- リポジトリ: `kuro-daei/research-notes`
- Aさんが作成したリポジトリに、Bさんを Collaborator として招待済み
- 2人ともローカルに clone 済み

---

### Aさんのフロー

Aさんは「気候変動に関する調査メモ」を作成します。

**ステップ 1: 最新状態を取り込む**

```bash
git pull origin main
```

**ステップ 2: 作業ブランチを作成する**

```bash
git checkout -b feature/climate-notes
```

**ステップ 3: ファイルを作成する**

`climate.md` というファイルを作成します。

```bash
# エディタで climate.md を作成（VS Code の場合）
code climate.md
```

`climate.md` の内容：

```markdown
# 気候変動メモ

## 調査日: 2026-03-08

- 世界平均気温は産業革命前比で +1.2°C 上昇（2023年時点）
- 2030年までに +1.5°C に達する可能性が高い
- 主な原因: 化石燃料の燃焼、森林破壊
```

**ステップ 4: コミットして push する**

```bash
git add climate.md
git commit -m "feat: 気候変動調査メモを追加"
git push -u origin feature/climate-notes
```

**ステップ 5: Pull Request を作成する**

GitHub を開き、「Compare & pull request」ボタンをクリックして PR を作成します。

- タイトル: `feat: 気候変動調査メモを追加`
- ベースブランチ: `main`
- 比較ブランチ: `feature/climate-notes`

---

### Bさんのフロー

Bさんは Aさんとほぼ同時に「エネルギー政策に関する調査メモ」を作成します。
（Aさんの PR がまだマージされていない状態で作業を開始します）

**ステップ 1: 最新状態を取り込む**

```bash
git pull origin main
```

> この時点では Aさんの変更はまだ main にマージされていないため、Bさんのローカルは Aさんの変更を含んでいません。

**ステップ 2: 作業ブランチを作成する**

```bash
git checkout -b feature/energy-notes
```

**ステップ 3: ファイルを作成する**

`energy.md` というファイルを作成します。

```bash
code energy.md
```

`energy.md` の内容：

```markdown
# エネルギー政策メモ

## 調査日: 2026-03-08

- 再生可能エネルギーの割合: 日本は約22%（2022年度）
- 太陽光・風力の導入コストは年々低下
- 原子力発電の再稼働議論が続いている
```

**ステップ 4: コミットして push する**

```bash
git add energy.md
git commit -m "feat: エネルギー政策調査メモを追加"
git push -u origin feature/energy-notes
```

**ステップ 5: Pull Request を作成する**

AさんのPRと同様に、GitHub から PR を作成します。

---

### Aさんの PR がマージされる

ここで Aさんの PR が先にマージされたとします。
`main` ブランチには `climate.md` が追加された状態になっています。

---

### コンフリクト発生と解決

Bさんが `energy.md` だけでなく `README.md` も同時に編集していた場合を考えます。
Aさんも同じ `README.md` を編集していてマージ済みだとすると、Bさんが PR を出したときにコンフリクトが発生します。

**コンフリクトが起きる原因（ASCII図）**

```
main (マージ後):  README.md → Aさんの変更が含まれている
                      ▲
Bさんのブランチ:  README.md → Bさんの変更が含まれている（同じ行を編集）

→ Git は「どちらの変更を採用すべきか」を自動判断できない = コンフリクト
```

**コンフリクトを解決する手順**

**ステップ 1: main の最新を自分のブランチに取り込む**

```bash
# Bさんの作業ブランチにいることを確認
git checkout feature/energy-notes

# main ブランチの最新変更を取り込む
git pull origin main
```

このとき、コンフリクトが発生すると以下のようなメッセージが表示されます：

```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

**ステップ 2: コンフリクトしているファイルを確認する**

```bash
git status
```

出力例：

```
On branch feature/energy-notes
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   README.md
```

**ステップ 3: コンフリクトマーカーを確認・編集する**

`README.md` を開くと、次のようなコンフリクトマーカーが挿入されています：

```
<<<<<<< HEAD
## 調査内容
- 気候変動メモを追加（Aさん）
=======
## 調査内容
- エネルギー政策メモを追加（Bさん）
>>>>>>> feature/energy-notes
```

マーカーの意味：

| マーカー | 意味 |
|---------|------|
| `<<<<<<< HEAD` | ここから下が「取り込んだ側（main）の内容」 |
| `=======` | 区切り線 |
| `>>>>>>> feature/...` | ここまでが「自分のブランチの内容」 |

**ステップ 4: コンフリクトを手動で解決する**

両方の変更を残したい場合：

```markdown
## 調査内容
- 気候変動メモを追加（Aさん）
- エネルギー政策メモを追加（Bさん）
```

コンフリクトマーカー（`<<<<<<<`、`=======`、`>>>>>>>`）をすべて削除し、最終的に残したい内容だけにします。

**ステップ 5: 解決後にコミットして push する**

```bash
git add README.md
git commit -m "fix: README のコンフリクトを解決"
git push origin feature/energy-notes
```

GitHub の PR ページを確認すると「This branch has no conflicts with the base branch」と表示され、マージ可能な状態になります。

**ステップ 6: PR をマージする**

GitHub 上で「Merge pull request」をクリックしてマージします。

---

### シナリオ全体の流れ（まとめ）

```
main ──●──────────────────────────●───────●── ...
       │                          ▲       ▲
       │ git pull                 │       │
       ▼                          │       │
Aさん: feature/climate ──commit── PR ─merge
       │
       │ git pull (ほぼ同時)
       ▼
Bさん: feature/energy ──commit── [コンフリクト解決] ── PR ─merge
```

---

## 4. git pull vs git fetch の使い分け

### 2つのコマンドの違い

`git fetch` と `git pull` はどちらもリモートの変更を取得するコマンドですが、動作が異なります。

#### git fetch

リモートの変更を**ダウンロードするだけ**で、ローカルのブランチには反映しません。

```bash
git fetch origin
```

```
リモート(origin)        ローカル
  main ──●──●──●   →   origin/main ──●──●──●  (更新される)
                              ↕
                         main ──●──●            (変わらない)
```

`git fetch` 後は `origin/main` が最新になりますが、`main` はそのままです。
変更内容を確認してから取り込みたいときに使います。

#### git pull

`git fetch` + `git merge` を一度に行います。

```bash
git pull origin main
```

```
リモート(origin)        ローカル
  main ──●──●──●   →   origin/main ──●──●──●  (更新)
                                                  ↓ merge
                         main ──●──●──●──●       (自動マージ)
```

リモートの変更をそのままローカルに反映します。

---

### ASCII 図で比較

```
【git fetch の流れ】

  GitHub                    ローカル PC
  ┌──────────────┐          ┌─────────────────────────────────┐
  │ origin/main  │          │ origin/main   main              │
  │  A─B─C─D    │  fetch   │  A─B─C         A─B─C            │
  │              │ ──────▶  │        ↓                        │
  │              │          │  A─B─C─D       A─B─C  ← 変わらない│
  └──────────────┘          └─────────────────────────────────┘

【git pull の流れ】

  GitHub                    ローカル PC
  ┌──────────────┐          ┌─────────────────────────────────┐
  │ origin/main  │          │ origin/main   main              │
  │  A─B─C─D    │   pull   │  A─B─C         A─B─C            │
  │              │ ──────▶  │  A─B─C─D       A─B─C─D         │
  │              │          │          ↑同時に main も更新     │
  └──────────────┘          └─────────────────────────────────┘
```

---

### どちらをいつ使うか

| 状況 | 推奨コマンド | 理由 |
|------|------------|------|
| 普段の作業で最新を取り込む | `git pull` | 1コマンドで完結、手軽 |
| 変更内容を確認してから取り込む | `git fetch` → 確認 → `git merge` | 差分を事前にチェックできる |
| チームのリモートブランチ一覧を更新する | `git fetch` | ローカルに影響を与えずに情報を更新 |
| コンフリクトのリスクを避けたい | `git fetch` → `git diff` → `git merge` | 安全に差分確認 |

**git fetch を使った安全な取り込み手順**

```bash
# 1. リモートの情報を取得する（ローカルには反映しない）
git fetch origin

# 2. 差分を確認する
git diff main origin/main

# 3. 問題なければマージする
git merge origin/main
```

**初心者のうちは `git pull` で十分です。**
チーム開発に慣れてきたら `git fetch` を使った安全な取り込みも試してみましょう。

---

### git pull のオプション

```bash
# 通常の pull（merge を使って取り込む）
git pull origin main

# rebase を使って取り込む（コミット履歴をきれいに保ちたい場合）
git pull --rebase origin main
```

`--rebase` を使うと、マージコミットが作られずにコミット履歴がすっきりします。
ただし、チームのルールに従って使い方を統一することが重要です。

---

## 5. リモート追跡ブランチ

### origin/main と main の違い

`git clone` をすると、ローカルには2種類のブランチが作られます：

| ブランチ名 | 種類 | 意味 |
|-----------|------|------|
| `main` | ローカルブランチ | 自分が直接作業するブランチ |
| `origin/main` | リモート追跡ブランチ | GitHub の `main` の状態を記録したコピー |

`origin/main` は GitHub 上の `main` ブランチをローカルで追跡するためのもので、
`git fetch` や `git pull` を実行したときに更新されます。

```
GitHub (origin)           ローカル PC
┌─────────────┐           ┌────────────────────────────┐
│ main        │  ←同期→  │ origin/main   （自動更新）  │
│  A─B─C─D   │           │  A─B─C─D                   │
└─────────────┘           │                            │
                           │ main          （手動管理）  │
                           │  A─B─C                    │
                           └────────────────────────────┘
```

- `origin/main` は「GitHub の main は今この状態」という記録
- `main` は「自分のローカルで今この状態」という作業場所
- `git pull` を実行すると、`origin/main` が更新されて `main` にも反映される

---

### `git branch -a` の出力の読み方

`git branch -a` コマンドを使うと、ローカルとリモートのすべてのブランチを確認できます。

```bash
git branch -a
```

出力例：

```
* main
  feature/climate-notes
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/feature/climate-notes
  remotes/origin/feature/energy-notes
```

各行の意味：

| 行 | 意味 |
|----|------|
| `* main` | 現在チェックアウトしているローカルブランチ（`*` が目印） |
| `feature/climate-notes` | ローカルブランチ |
| `remotes/origin/HEAD -> origin/main` | GitHub のデフォルトブランチを示すポインタ |
| `remotes/origin/main` | GitHub の `main` ブランチのリモート追跡ブランチ |
| `remotes/origin/feature/...` | GitHub 上に存在するリモートブランチの追跡コピー |

`remotes/origin/` で始まる行がリモート追跡ブランチです。
これらは直接編集できず、`git fetch` や `git pull` で自動的に更新されます。

---

### 追跡ブランチの仕組み

ブランチに「追跡ブランチ」が設定されていると、`git pull` や `git push` で `origin main` を省略できるようになります。

**追跡ブランチを設定する（-u フラグ）**

```bash
# -u フラグをつけて push すると自動的に追跡ブランチが設定される
git push -u origin feature/my-branch
```

この `-u` は `--set-upstream` の略です。一度設定すると、以降は以下のように省略できます：

```bash
# 追跡ブランチを設定済みの場合
git push      # git push origin feature/my-branch と同じ
git pull      # git pull origin feature/my-branch と同じ
```

**追跡ブランチの設定を確認する**

```bash
git branch -vv
```

出力例：

```
* main                  a1b2c3d [origin/main] feat: README を更新
  feature/climate-notes e4f5g6h [origin/feature/climate-notes] feat: 気候変動メモ追加
```

`[origin/main]` や `[origin/feature/climate-notes]` が追跡ブランチを示しています。

**追跡ブランチを手動で設定する**

```bash
git branch --set-upstream-to=origin/main main
```

---

### よく使うリモート関連コマンドまとめ

```bash
# リモートリポジトリの一覧を確認
git remote -v

# リモートの情報を取得（ローカルには反映しない）
git fetch origin

# リモートの変更をローカルに取り込む
git pull origin main

# リモートにブランチを push して追跡設定も行う
git push -u origin feature/my-branch

# ローカルとリモート全てのブランチを表示
git branch -a

# 追跡ブランチの詳細を確認
git branch -vv

# リモートで削除済みのブランチをローカルの一覧から消す
git fetch --prune
```

---

## まとめ

このドキュメントで学んだこと：

1. **チーム開発の基本構造** — 全員が同じリモートリポジトリを共有し、ブランチで作業を分ける
2. **Collaborator の追加** — GitHub の Settings → Collaborators から招待する
3. **シナリオ演習** — 2人が同時に作業してコンフリクトが発生した場合の解決手順
4. **git fetch vs git pull** — fetch はダウンロードのみ、pull はマージまで行う
5. **リモート追跡ブランチ** — `origin/main` は GitHub の状態を記録したローカルのコピー

チーム開発では「こまめに pull して最新を保つ」「ブランチで作業を分ける」「PR でレビューしてからマージする」の3点を意識することがトラブル防止の基本です。

---

[90_command_reference.md](./90_command_reference.md) — コマンドリファレンス早見表
