# 08. GitHub に公開（チーム）

**Claude は起動したまま、この章の内容を進めてください。**

07 章でチームがそれぞれ `apple/info/` にファイルを作成しました。
この章では、ブランチを使った安全な push と Pull Request の基本を体験します。

---

## 目次

1. [Git の基本概念（復習）](#1-git-の基本概念復習)
2. [Claude の中での作業](#2-claude-の中での作業)
3. [まとめ](#3-まとめ)

---

## 1. Git の基本概念（復習）

操作を始める前に、3つの概念を確認します。

### ファイルが通る4つの領域

```
ワーキングツリー ─git add─▶ ステージ ─git commit─▶ ローカルRepo ─git push─▶ GitHub
（編集中のファイル）          （コミット予告）       （確定した履歴）             （公開）
```

| 領域 | 役割 |
|---|---|
| ワーキングツリー | 実際に編集するファイル群 |
| ステージ | コミットに含める内容を選ぶ一時置き場 |
| ローカルリポジトリ | commit で確定した履歴が積み重なる場所 |
| GitHub（リモート） | push で公開・共有される場所 |

### コミットとは

コミットは「その時点の全ファイルのスナップショット」を名前付きで記録する操作です。
積み重ねることで「いつ・何を変えたか」が追跡できるようになります。

```
  ●─────●─────●─────●  ← main
  A     B     C     D
古い  ─── 時間の流れ ───▶  新しい
```

### ブランチとは

ブランチは「作業用の分岐」です。main を安定させたまま、別の流れで作業できます。

```
main ──────────────────────────────── (安定版)
         └── feature/add-overview ───┐
                 (作業ブランチ)       │
                                     ▼
                               PR → マージ → main に統合
```

---

## 2. Claude の中での作業

### Step 1：作業ブランチを作る

各自が担当ファイルを push するためのブランチを作ります。

**Aさんの場合（企業概要を追加）:**

```
> main を最新にして、feature/add-overview というブランチを作って
```

```
git switch main を実行
git pull を実行
✓ Already up to date.

git switch -c feature/add-overview を実行
✓ ブランチ feature/add-overview に切り替えました
```

**Bさんの場合（担当者情報を追加）:**

```
> main を最新にして、feature/add-contacts というブランチを作って
```

```
【Step 1 完了後の状態】
Aさんのローカル                          Bさんのローカル
  main: ●                                main: ●
  └── feature/add-overview ← HEAD        └── feature/add-contacts ← HEAD

GitHub (main): ●  ← まだ変更なし
```

### Step 2：担当ファイルをコミットする

**Aさんの場合:**

```
> info/overview.md をコミットして。メッセージは「feat: Apple の企業概要を追加」で
```

```
git add info/overview.md
git status を確認中...

Changes to be committed:
  new file: info/overview.md

git commit -m "feat: Apple の企業概要を追加" を実行します。
✓ [feature/add-overview 3a9f1bc] feat: Apple の企業概要を追加
```

**Bさんの場合:**

```
> info/contacts.md をコミットして。メッセージは「feat: 担当者情報テンプレートを追加」で
```

```
【Step 2 完了後の状態】
Aさんのローカル                            Bさんのローカル
  main: ●                                  main: ●
  feature/add-overview: ●─●               feature/add-contacts: ●─●
                          ↑                                        ↑
                   overview.md 追加                         contacts.md 追加

GitHub (main): ●  ← まだ push されていない
```

### Step 3：push して PR を作る

**Aさんの場合:**

```
> push して PR を作って
```

```
git push -u origin feature/add-overview を実行します...
✓ ブランチを push しました

PR を作成します：
タイトル: feat: Apple の企業概要を追加
ベース: main ← feature/add-overview

作成しますか？ [y/n]
```

`y` を押すと PR が作成されます。

```
✓ PR #1 を作成しました
  https://github.com/[ユーザー名]/apple/pull/1
```

**Bさんの場合も同様に:**

```
> push して PR を作って
```

```
【Step 3 完了後の状態】
GitHub
  main: ●
  feature/add-overview: ●─●  ← push 済み  🔀 PR #1（Aさん: overview.md 追加）
  feature/add-contacts: ●─●  ← push 済み  🔀 PR #2（Bさん: contacts.md 追加）
```

### Step 4：お互いの PR を確認する

PR が作成されたら、チームメンバーが内容を確認します。

```
> PR の一覧を見せて
```

```
#1  feat: Apple の企業概要を追加        [feature/add-overview]  - Aさん
#2  feat: 担当者情報テンプレートを追加    [feature/add-contacts]  - Bさん
```

相手の PR の内容を確認してみましょう。

```
> PR #1 の内容を見せて
```

問題なければコメントを残します。

```
> PR #1 に「内容確認しました。LGTM!」とコメントして
```

### Step 5：PR をマージして後片付けする

```
> PR #1 をマージして、ブランチも削除して
```

```
以下を実行します：
1. PR #1 をマージ
2. リモートブランチ feature/add-overview を削除
3. git switch main && git pull でローカルを同期

実行しますか？ [y/n]
```

```
✓ PR #1 をマージしました
✓ リモートブランチを削除しました
✓ ローカル main を最新にしました
```

```
【Step 5 完了後の状態】
GitHub (main)                                全員のローカル (main) ← pull で同期済み
  ●──────────────────────────●                ●──────────────────────────●
                              ↑                info/overview.md ★
                    PR #1 マージ済み           info/contacts.md ★
  feature/add-overview: 削除済み
```

GitHub のリポジトリページを開いて、`info/overview.md` が main に追加されているか確認してみましょう。

---

## 3. まとめ

| ステップ | Claude への指示例 |
|---|---|
| ブランチ作成 | `> main を最新にして、feature/〇〇 ブランチを作って` |
| コミット | `> info/overview.md をコミットして。メッセージは「feat: 〇〇」で` |
| push & PR | `> push して PR を作って` |
| PR 確認 | `> PR #1 の内容を見せて` |
| コメント | `> PR #1 に「LGTM!」とコメントして` |
| マージ・後片付け | `> PR をマージしてブランチを削除して` |

> **Git コマンドの詳細を知りたい場合:**
> [supplement/claude/03_git_flow.md](./supplement/claude/03_git_flow.md) — 各コマンドの意味と詳細
> [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) — HEAD・git diff・git log の読み方

---

← [前の章: チームでリサーチ](./10_research_team.md) ｜ [次の章: Markdown 記法 →](./12_markdown.md)
