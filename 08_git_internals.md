# 05. Git の基本概念

**Claude は起動したまま、この章の内容を確認してください。**
コマンドの実行は Claude に依頼できます（例: `git status を実行して`）。

04 章では Claude で博報堂の情報を整理し、`hakuhodo/info/` にファイルを保存しました。
この章では、Claude が裏で何をしているかを理解するための基本概念を整理します。

---

## 4つの領域とコマンドの対応

```
ワーキングツリー ─git add─▶ ステージ ─git commit─▶ ローカルRepo ─git push─▶ GitHub
```

| 領域 | 役割 |
|---|---|
| ワーキングツリー | 実際に編集するファイル群 |
| ステージ（Index） | `git add` したファイルの一時置き場 |
| ローカルリポジトリ | `git commit` で確定した履歴が積み重なる場所 |
| リモート（GitHub） | `git push` で公開・共有される場所 |

---

## コミット・ブランチ・HEAD

```
  ●─────●─────●─────●  ← main（最新コミットを指す）
  A     B     C     D
```

- **コミット**: その時点の全ファイルのスナップショット。積み重ねが履歴になる
- **ブランチ**: コミットへのポインタ。`main` は常に最新コミットを指す
- **HEAD**: 「今自分がいる場所」を示すポインタ。`git switch` でブランチを変えると移動する

---

## git status で確認できる3状態

| 状態 | 意味 |
|---|---|
| Untracked | 新規作成で Git が未管理 |
| Modified | 変更済み・未 add |
| Staged | `git add` 済み・次のコミット待ち |

Claude に「git status を実行して」と依頼するか、ターミナルで直接実行できます。

```bash
git status    # 現在の状態を確認
```

---

## hakuhodo リポジトリでの動き

04 章で `info/overview.md` と `info/contacts.md` を作成した後の状態をイメージします。

```
[ワーキングツリー]
  info/overview.md   ← Untracked（まだ Git が知らないファイル）
  info/contacts.md   ← Untracked

       │ git add info/
       ▼

[ステージ]
  info/overview.md   ← Staged（コミット待ち）
  info/contacts.md   ← Staged

       │ git commit -m "feat: 博報堂の基本情報を追加"
       ▼

[ローカルリポジトリ]
  コミット A: 博報堂の基本情報を追加

       │ git push
       ▼

[GitHub]
  公開・共有された状態
```

Claude に次のように依頼するだけで、この一連の操作を実行してもらえます。

```
> info/overview.md と info/contacts.md をコミットして。
  メッセージは「feat: 博報堂の基本情報を追加」で
```

---

> これらのコマンドは Claude に「git log --oneline を実行して」のように依頼すると実行してもらえます。

## よく使うコマンド早見表

| コマンド | 役割 |
|---|---|
| `git status` | ファイルの状態を確認 |
| `git add <file>` | ステージに追加 |
| `git commit -m "..."` | 変更を履歴に記録 |
| `git push` | GitHub に送る |
| `git pull` | GitHub から取り込む |
| `git switch <branch>` | ブランチを切り替える（HEAD を移動） |
| `git switch -c <branch>` | 新しいブランチを作成して切り替える |
| `git log --oneline` | コミット履歴を一覧表示 |

---

## さらに詳しく知りたい場合

- [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) — git diff・git log の読み方・.git 内部構造・detached HEAD など

---

← [前の章: Claude でリサーチ](./07_research_with_claude.md) ｜ [次の章: GitHub チーム招待 →](./09_team_invite.md)
