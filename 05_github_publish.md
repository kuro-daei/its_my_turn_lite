# 05. リサーチ結果を GitHub に公開する

04 章でまとめた調査メモを、GitHub に公開します。
Git の基本概念を押さえたら、あとはすべて Claude に操作を任せましょう。

---

## 目次

1. [Git の基本概念](#1-git-の基本概念)
2. [bash フェーズ：Claude を起動する](#2-bash-フェーズclaude-を起動する)
3. [Claude の中での作業](#3-claude-の中での作業)
4. [まとめ](#4-まとめ)

---

## 1. Git の基本概念

操作を始める前に、3つの概念だけ押さえておきます。

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
         └── docs/initial-research ──┐
                 (作業ブランチ)       │
                                     ▼
                               PR → マージ → main に統合
```

---

## 2. bash フェーズ：Claude を起動する

```bash
cd ~/works/client-research
claude
```

これだけです。以降の Git・GitHub 操作はすべて Claude に任せます。

---

## 3. Claude の中での作業

### Step 1：GitHub にリポジトリを作る

```
> GitHub にリポジトリを作って。名前は client-research、公開で、README 付きで
```

```
gh repo create client-research --public --description "クライアント調査メモ" --add-readme
を実行します。よいですか？ [y/n]
```

`y` を押すと作成されます。

```
✓ Created repository yourname/client-research on GitHub
  https://github.com/yourname/client-research
```

### Step 2：既存フォルダを GitHub と接続する

```
> このフォルダを今作った GitHub リポジトリと接続して、notes/ の内容を push して
```

```
以下の手順で進めます：
1. git init
2. git remote add origin git@github.com:yourname/client-research.git
3. git add README.md notes/
4. git commit -m "feat: 調査メモの初期追加"
5. git push -u origin main

実行しますか？ [y/n]
```

`y` を押すと完了します。

```
✓ ブランチ main を GitHub に push しました
```

### Step 3：作業ブランチを作って調査メモを追加する

04 章でまとめた調査メモを、ブランチ経由でコミットします。

```
> docs/financial-research というブランチを作って
```

```
git checkout -b docs/financial-research を実行します。
✓ ブランチ docs/financial-research に切り替えました
```

```
> notes/financial-research.md をコミットして。メッセージは「docs: 財務諸表リサーチを追加」で
```

```
git add notes/financial-research.md
git status を確認中...

Changes to be committed:
  new file: notes/financial-research.md

git commit -m "docs: 財務諸表リサーチを追加" を実行します。
✓ [docs/financial-research 3a9f1bc] docs: 財務諸表リサーチを追加
```

### Step 4：push して PR を作る

```
> push して PR を作って
```

```
git push -u origin docs/financial-research を実行します...
✓ ブランチを push しました

PR を作成します：
タイトル: docs: 財務諸表リサーチを追加
ベース: main ← docs/financial-research

作成しますか？ [y/n]
```

```
✓ PR #1 を作成しました
  https://github.com/yourname/client-research/pull/1
```

### Step 5：PR をマージして後片付けする

```
> PR をマージして、ブランチも削除して
```

```
以下を実行します：
1. PR #1 をマージ
2. リモートブランチ docs/financial-research を削除
3. git checkout main && git pull でローカルを同期

実行しますか？ [y/n]
```

```
✓ PR #1 をマージしました
✓ リモートブランチを削除しました
✓ ローカル main を最新にしました
```

GitHub のリポジトリページを開いて、`notes/financial-research.md` が main に追加されているか確認してみましょう。

---

## 4. まとめ

| ステップ | Claude への指示例 |
|---|---|
| リポジトリ作成 | 「GitHub にリポジトリを作って」 |
| フォルダ接続・初回 push | 「このフォルダをリポジトリと接続して push して」 |
| ブランチ作成 | 「〇〇というブランチを作って」 |
| コミット | 「〇〇をコミットして。メッセージは△△で」 |
| push & PR | 「push して PR を作って」 |
| マージ・後片付け | 「PR をマージしてブランチを削除して」 |

> **Git コマンドの詳細を知りたい場合:**
> [supplement/claude/03_git_flow.md](./supplement/claude/03_git_flow.md) — 各コマンドの意味と詳細
> [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) — HEAD・git diff・git log の読み方

---

次の章: [06_git_internals.md](./06_git_internals.md) — Git の基本概念（詳細版）
