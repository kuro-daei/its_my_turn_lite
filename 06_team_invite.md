# 06. GitHub チーム招待

ここからはフェーズ2です。**2〜3人チームで `apple` リポジトリを共有**し、
Apple 社の情報をチームで分担してリサーチ・管理します。

---

## 目次

1. [なぜチームでリポジトリを共有するのか](#1-なぜチームでリポジトリを共有するのか)
2. [ターミナル：リポジトリを作る](#2-ターミナルリポジトリを作る)
3. [Claude の中での作業（Collaborator 招待）](#3-claude-の中での作業collaborator-招待)
4. [チームメンバー側の作業（クローン）](#4-チームメンバー側の作業クローン)
5. [まとめ](#5-まとめ)

---

## 1. なぜチームでリポジトリを共有するのか

フェーズ1（博報堂）ではひとりで作業しました。
フェーズ2（Apple）では **複数人が同じリポジトリを触る** ことを体験します。

チームでリポジトリを共有するメリット：

- **分担作業**: Aさんが企業概要、Bさんが担当者情報を同時に作成できる
- **変更の追跡**: 「誰が・いつ・何を変えたか」が Git の履歴で分かる
- **レビュー**: Pull Request でお互いの変更を確認し合える
- **バックアップ**: GitHub にあるのでローカルが壊れても安心

### チームの役割分担

| 役割 | 担当 | 作業 |
|---|---|---|
| オーナー | リポジトリを作成する1人 | `apple` リポジトリ作成・チームを招待 |
| コラボレーター | 招待を受ける残りのメンバー | クローン・ブランチで作業 |

---

## 2. ターミナル：リポジトリを作る

> **⚠️ コマンドライン操作:** この手順はターミナルで実行します。**オーナー役の1人**が実行してください。

まず作業ディレクトリを作成し、Claude を起動します。

```bash
mkdir ~/works/apple
cd ~/works/apple
claude
```

起動できたら、以降の操作はすべて Claude 内で行います。

---

## 3. Claude の中での作業（Collaborator 招待）

**Claude は起動したまま、この章の内容を進めてください。**

### Step 1：GitHub にリポジトリを作る

```
> apple というリポジトリを GitHub に作って。公開設定で
```

Claude が次のように確認してきます。

```
gh repo create apple --public を実行します。よいですか？ [y/n]
```

`y` を押すと作成されます。

```
✓ Created repository [あなたのユーザー名]/apple on GitHub
  https://github.com/[あなたのユーザー名]/apple
```

### Step 2：リポジトリを初期化して push する

```
> このフォルダを今作った GitHub リポジトリと接続して、README.md を作って push して
```

```
以下の手順で進めます：
1. git init
2. README.md を作成
3. git add README.md
4. git commit -m "feat: 初期コミット"
5. git remote add origin git@github.com:[ユーザー名]/apple.git
6. git push -u origin main

実行しますか？ [y/n]
```

`y` を押すと完了します。

### Step 3：Collaborator を招待する

チームメンバーの GitHub ユーザー名を確認してから、Claude に依頼します。

```
> [チームメンバーのGitHubユーザー名] を Collaborator として招待して
```

Claude が GitHub の Collaborator 招待を実行します。

```
gh api repos/[ユーザー名]/apple/collaborators/[メンバー名] --method PUT を実行します。
✓ [メンバー名] を Collaborator として招待しました
```

チームメンバー全員分、繰り返し依頼してください。

> **招待メール**: 招待されたメンバーには GitHub からメールが届きます。
> メール内の「Accept invitation」ボタンを押すと参加完了です。

---

## 4. チームメンバー側の作業（クローン）

> **⚠️ コマンドライン操作:** 招待を受けたメンバー全員がターミナルで実行します。

招待メールの「Accept invitation」を押して参加完了後、リポジトリをクローンします。

```bash
git clone git@github.com:[オーナーのユーザー名]/apple.git ~/works/apple
cd ~/works/apple
claude
```

クローン完了後、Claude が起動します。フォルダの内容を確認してみましょう。

```
> このフォルダの中身を見せて
```

```
apple/
└── README.md
```

オーナーが作った `README.md` が取得できていれば成功です。

---

## 5. まとめ

| ステップ | 担当 | 操作 |
|---|---|---|
| `apple` リポジトリ作成 | オーナー | `> apple というリポジトリを GitHub に作って` |
| Collaborator 招待 | オーナー | `> [ユーザー名] を Collaborator として招待して` |
| 招待受諾 | メンバー全員 | GitHub メールの「Accept invitation」をクリック |
| クローン | メンバー全員 | `git clone git@github.com:...` をターミナルで実行 |
| Claude 起動 | メンバー全員 | `cd ~/works/apple && claude` |

全員が同じリポジトリを操作できる環境が整いました。
次章では、この `apple` リポジトリを使ってチームで情報収集を分担します。

---

次の章: [07_research_team.md](./07_research_team.md) — チームでリサーチ
