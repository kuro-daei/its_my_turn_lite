# 06. Git の基本概念

05 章では Claude に Git 操作を任せました。
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

```bash
git status    # 現在の状態を確認
```

---

## よく使うコマンド早見表

| コマンド | 役割 |
|---|---|
| `git status` | ファイルの状態を確認 |
| `git add <file>` | ステージに追加 |
| `git commit -m "..."` | 変更を履歴に記録 |
| `git push` | GitHub に送る |
| `git pull` | GitHub から取り込む |
| `git log --oneline` | コミット履歴を一覧表示 |

---

## さらに詳しく知りたい場合

- [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) — git diff・git log の読み方・.git 内部構造・detached HEAD など

---

次の章: [07_markdown.md](./07_markdown.md) — Markdown 記法
