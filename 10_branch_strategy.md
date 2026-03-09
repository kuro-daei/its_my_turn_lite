# 10. ブランチ戦略

複数人で同じリポジトリを触るとき、「誰がどのファイルを編集しているか」を管理しないと衝突が起きます。
このドキュメントでは、`apple` リポジトリをチームで安全に運用するためのブランチ戦略を学びます。

**Claude は起動したまま、この章の実践を進めてください。**
ブランチ操作・コミット・push はすべて Claude に依頼できます。

---

## 目次

1. [なぜブランチ戦略が必要か](#1-なぜブランチ戦略が必要か)
2. [GitHub Flow（シンプル・推奨）](#2-github-flow)
3. [ブランチ命名規則](#3-ブランチ命名規則)
4. [main ブランチを守るルール（Branch Protection Rules）](#4-main-ブランチを守るルール)
5. [実践：GitHub Flow で apple/info/ を分担編集する](#5-実践github-flow-で-appleinfo-を分担編集する)

---

## 1. なぜブランチ戦略が必要か

### 複数人が同時に作業すると何が起きるか

たとえば 3 人が `apple` リポジトリに参加し、それぞれが `main` ブランチに直接 push したとします。

```
（ある日の午後）

Aさん  ──push──▶  main  ← info/overview.md を更新
Bさん  ──push──▶  main  ← Aさんの変更を上書きしてしまった！
Cさん  ──push──▶  main  ← Bさんが消した内容をさらに上書き…
```

こうなると：

- 誰かの変更が消える
- 「いつ・誰が・何を変えたか」が追いにくくなる
- バグが混入しても、どの変更が原因か分からない

### ブランチを使うとどう変わるか

```
          ┌── feature/aさん-overview-update ──┐
          │                                    │
main ─────┤                                    ├──── main（安全な状態を維持）
          │                                    │
          └── feature/bさん-contacts-update ───┘
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
    └──▶ feature/contacts-update   ← ここで作業する


2. 作業ブランチで変更・commit を重ねる

   feature/contacts-update
    ├── commit: "feat: 連絡先を追加"
    └── commit: "fix: メールアドレスを修正"


3. GitHub に push して Pull Request を開く

   feature/contacts-update ──push──▶ GitHub
                                         │
                                      Pull Request 作成
                                      （レビュー・確認）


4. レビューが通ったら main にマージ

   feature/contacts-update ──merge──▶ main


5. 作業ブランチを削除

   feature/contacts-update を削除（main にマージ済みのため不要）
```

### 各ステップの詳細

#### ステップ 1: main から作業ブランチを作る

まず `main` を最新の状態にしてから、新しいブランチを作ります。

```
> main を最新にして、feature/update-contacts ブランチを作って
```

#### ステップ 2: 変更を commit する

ファイルを編集したら、Claude に次のように依頼します：

```
> info/contacts.md をコミットして。メッセージは「feat: 担当者を追加」で
```

#### ステップ 3: GitHub に push して PR を開く

```
> push して PR を作って
```

Pull Request の詳細な操作は [11_pull_request.md](./11_pull_request.md) で解説します。

#### ステップ 4: マージ

チームメンバーがレビューして問題なければ：

```
> PR をマージして
```

#### ステップ 5: ブランチ削除

```
> PR をマージして、ブランチも削除して
```

---

## 3. ブランチ命名規則

ブランチ名はチーム全員が読むものです。「何のためのブランチか」が名前から分かると、作業の見通しが良くなります。

### 命名パターン

| プレフィックス | 用途 | 例 |
|---|---|---|
| `feature/` | 新機能・新コンテンツの追加 | `feature/add-products-info` |
| `fix/` | バグ修正・誤字修正 | `fix/contacts-email-typo` |
| `docs/` | ドキュメントのみの更新 | `docs/overview-update` |
| `refactor/` | 構成変更（内容変更なし） | `refactor/info-folder-restructure` |

### 命名のコツ

```
feature/[説明]
         └─── 何をするブランチか、1〜3 語で書く
```

**良い例**

```
feature/add-products-info
fix/contacts-email-typo
docs/overview-2025-update
```

**避けたい例**

```
test          ← 何のブランチか分からない
my-branch     ← 誰でも付けられる名前
temp          ← 永遠に "temp" のまま残りがち
```

### `apple` リポジトリで使うルール

```
feature/  ─ 新しいファイル・情報の追加
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
| 直接 push の禁止 | ローカルから `git push` で直接 main に書けない |
| 強制 push の禁止 | `git push --force` を禁止 |

### GitHub Web UI での設定手順

#### 手順 1: リポジトリの Settings を開く

```
GitHub の apple リポジトリページ
  └── [Settings] タブをクリック
```

#### 手順 2: Branches メニューを開く

```
左サイドバー
  └── [Branches] をクリック
```

#### 手順 3: ルールを追加する

```
Branch protection rules セクション
  └── [Add branch protection rule] ボタンをクリック
```

#### 手順 4: ルールを設定する

```
Branch name pattern: main

チェックを入れる項目:
  [x] Require a pull request before merging
        └── Required number of approvals before merging: 1

  [x] Do not allow bypassing the above settings
```

#### 設定後の動作確認

設定後に `git push origin main` を試みると：

```
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Changes must be made through a pull request.
```

このエラーが出れば正常です。`main` への変更は必ず PR を通す必要があります。

---

## 5. 実践：GitHub Flow で apple/info/ を分担編集する

ここまでの知識を使って、チームで `apple/info/` のファイルを分担して更新します。

### シナリオ

「Apple 担当者に変更があったため、`contacts.md` を更新する」（Bさんの作業）
「企業概要に最新の売上情報を追記する」（Aさんの作業）

### ステップ 0: 準備（main を最新にする）

```
> main を最新にして
```

### ステップ 1: 作業ブランチを作る

**Aさん:**

```
> feature/update-overview ブランチを作って
```

**Bさん:**

```
> feature/update-contacts ブランチを作って
```

### ステップ 2: ファイルを編集する

**Bさん（contacts.md を更新する例）:**

```
> info/contacts.md を開いて、山田さんの連絡先を以下に更新して：
  メール: yamada-new@apple.com
  電話: 03-XXXX-YYYY
```

Claude が指定した内容でファイルを更新します。

### ステップ 3: commit する

```
> info/contacts.md をコミットして。メッセージは「fix: 山田さんの連絡先を更新」で
```

### ステップ 4: push して PR を作る

```
> push して PR を作って
```

### ステップ 5: チームメンバーが PR をレビューする

相手の PR に確認コメントを残します。

```
> PR #3 の内容を見せて
```

```
> PR #3 に「連絡先の更新を確認しました。LGTM!」とコメントして
```

### ステップ 6: マージ後のブランチ削除

```
> PR をマージして、ブランチを削除して、main を最新にして
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

次の章: [11_pull_request.md](./11_pull_request.md) — Pull Request ワークフロー
