# 04. 調査結果を GitHub に公開する

前章では Markdown の書き方を学びました。
この章では、書いた調査メモを **GitHub に公開するまでの完全なワークフロー** を手を動かしながら体験します。

---

## 全体フロー図

```
┌─────────────────────────────────────────────────────────────────────┐
│                  調査結果を GitHub に公開するフロー                      │
└─────────────────────────────────────────────────────────────────────┘

  [GitHub Web UI]          [ローカル（WSL）]            [GitHub]
       │                         │                         │
  1. リポジトリ作成  ──────────────────────────────────────▶ リポジトリ誕生
       │                         │                         │
       │              2. git clone  ◀──────────────────────┤
       │                   │                               │
       │              3. ブランチ作成                        │
       │            (git checkout -b docs/research-xxx)    │
       │                   │                               │
       │              4. Markdown 作成                      │
       │              (VS Code でファイルを書く)              │
       │                   │                               │
       │              5. commit                            │
       │              (git add → git status → git commit)  │
       │                   │                               │
       │              6. push  ──────────────────────────▶ ブランチが届く
       │                   │                               │
       │              7. PR 作成  ──────────────────────▶ Pull Request 誕生
       │                   │                               │
       │              8. PR マージ  ◀────────────────────── main に統合！
       │                   │                               │
       │              9. 後片付け                           │
       │              (ブランチ削除 → git pull)              │
       │                   │                               │
       └─────────────────────────────────── GitHub 公開完了！ ──┘
```

---

## 1. GitHub でリポジトリを作成する

### なぜリポジトリが必要か

Git のリポジトリ（repository）は、ファイルとその変更履歴をまとめて保管する「倉庫」です。
GitHub にリポジトリを作ることで、クラウドにバックアップができ、他者と共有できるようになります。

### 方法 A: Web UI で作成する

1. [github.com](https://github.com) にログインする
2. 右上の「+」ボタン → 「New repository」をクリック
3. 以下を入力する

   | 項目 | 入力例 | 説明 |
   |------|--------|------|
   | Repository name | `my-research-notes` | リポジトリ名（英数字・ハイフン推奨） |
   | Description | `調査メモを公開するリポジトリ` | 任意。何のリポジトリかを書く |
   | Visibility | `Public` | 公開。学習用は Public で OK |
   | Initialize this repository with a README | チェックを入れる | README を最初から作る |

4. 「Create repository」ボタンをクリック

> **ポイント:** "Initialize with README" にチェックを入れると、すぐに `git clone` できる状態になります。チェックを入れないと空のリポジトリが作成され、最初の push に追加手順が必要です。

### 方法 B: `gh repo create` コマンドで作成する

GitHub CLI（`gh`）を使うと、コマンドラインだけでリポジトリを作れます。

```bash
gh repo create my-research-notes \
  --public \
  --description "調査メモを公開するリポジトリ" \
  --add-readme
```

実行結果の例:

```
✓ Created repository kuro-daei/my-research-notes on GitHub
  https://github.com/kuro-daei/my-research-notes
```

> **`gh` がインストールされていない場合:**
> ```bash
> # Ubuntu/WSL の場合
> sudo apt install gh
> gh auth login   # GitHub アカウントで認証
> ```

---

## 2. ローカルにクローンする

### なぜクローンするのか

GitHub 上にあるリポジトリは「リモートリポジトリ」です。
ファイルを編集するには、手元のパソコン（WSL）に「ローカルリポジトリ」としてコピーする必要があります。
この操作を **clone（クローン）** と呼びます。

### 方法 A: `git clone` でクローンする

GitHub のリポジトリページを開き、「Code」ボタンから SSH の URL をコピーします。

```
git@github.com:kuro-daei/my-research-notes.git
```

WSL 上でクローンを実行します。

```bash
cd ~
git clone git@github.com:kuro-daei/my-research-notes.git
```

実行結果の例:

```
Cloning into 'my-research-notes'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

### 方法 B: `gh repo clone` でクローンする

```bash
gh repo clone kuro-daei/my-research-notes
```

### クローン後の確認

クローンが完了したら、正しくファイルが取得できているか確認しましょう。

```bash
cd my-research-notes
pwd
```

出力例:

```
/home/kuro-daei/my-research-notes
```

```bash
ls -la
```

出力例:

```
total 16
drwxr-xr-x 3 kuro-daei kuro-daei 4096 Mar  8 12:00 .
drwxr-xr-x 8 kuro-daei kuro-daei 4096 Mar  8 12:00 ..
drwxr-xr-x 8 kuro-daei kuro-daei 4096 Mar  8 12:00 .git
-rw-r--r-- 1 kuro-daei kuro-daei   27 Mar  8 12:00 README.md
```

`.git` ディレクトリが存在すれば、Git リポジトリとして正しく認識されています。

```bash
git log --oneline
```

出力例:

```
a1b2c3d (HEAD -> main, origin/main) Initial commit
```

> **SSH 接続エラーが出た場合:**
> `Permission denied (publickey)` というエラーが出たら、SSH 鍵の設定が必要です。
> `CLAUDE.md` の「SSH 設定」セクションを参照してください。

---

## 3. 作業ブランチを作る

### なぜ main を直接編集しないのか

`main` ブランチはプロジェクトの「完成品」を置く場所です。
直接 `main` を編集すると、作業途中の未完成なファイルが公開されたり、複数人で作業するときに衝突が起きやすくなります。

**ブランチを使うメリット:**

- `main` を常にきれいな状態に保てる
- 作業中のミスが `main` に影響しない
- 複数の作業を並行して進められる
- Pull Request でレビューを受けてからマージできる

```
main ──────────────────────────────────────── (安定版)
         └── docs/research-claude ──────┐
                 (作業ブランチ)          │
                                        ▼
                                   PR → マージ → main に統合
```

### ブランチを作成して切り替える

```bash
git checkout -b docs/research-claude
```

実行結果の例:

```
Switched to a new branch 'docs/research-claude'
```

現在のブランチを確認します。

```bash
git branch
```

出力例:

```
* docs/research-claude
  main
```

`*` が付いているのが現在のブランチです。

> **ブランチ命名のルール:**
> このプロジェクトでは以下の命名規則を使います。
>
> | パターン | 用途 | 例 |
> |----------|------|----|
> | `feature/機能名` | 新機能・新コンテンツ | `feature/search-function` |
> | `fix/バグ内容` | 誤字・誤記修正 | `fix/typo-in-setup` |
> | `docs/更新内容` | ドキュメント追加・更新 | `docs/research-claude` |

---

## 4. 調査メモを Markdown で書く

### VS Code でファイルを作成する

VS Code を使ってリポジトリフォルダを開きます。

```bash
code .
```

VS Code が開いたら、左のエクスプローラーで「新しいファイル」アイコンをクリックし、
`research_claude.md` というファイルを作成します。

### 前章の内容を実践として書く

前章（04_markdown.md）で学んだ Markdown 記法を使って、調査メモを書きましょう。
以下はテンプレートです。

```markdown
# Claude について調べてみた

## 調査日

2026-03-08

## Claude とは

Anthropic が開発した AI アシスタントです。
主に文章生成・コード補助・質問応答などに使われています。

## 特徴

- 安全性を重視した設計（Constitutional AI）
- 長い文脈（コンテキスト）を処理できる
- 日本語にも対応

## 参考リンク

- [Anthropic 公式サイト](https://www.anthropic.com)
- [Claude 使い方ガイド](https://claude.ai)

## 感想

実際に使ってみると、複雑な質問にも丁寧に答えてくれた。
ドキュメント作成の補助として活用できそう。
```

> **ポイント:** 内容は自分が実際に調査した内容に書き換えてください。
> 「自分の言葉で書く」ことが、学習効果を高めます。

---

## 5. commit する

### commit とは何か

**commit（コミット）** は、ファイルの変更内容に「名前」を付けて Git の履歴に記録する操作です。
コミットを積み重ねることで、いつ・何を・なぜ変更したかが追跡できるようになります。

### Step 1: ステージに追加する（`git add`）

```bash
git add research_claude.md
```

> **`git add` の意味:**
> 変更したファイルを「次のコミットに含める」と宣言する操作です。
> ステージング（staging）とも呼ばれます。

複数ファイルを一括追加したい場合:

```bash
git add .   # カレントディレクトリ以下のすべての変更を追加
```

### Step 2: 状態を確認する（`git status`）

コミットの前に、何が追加されているかを必ず確認しましょう。

```bash
git status
```

出力例:

```
On branch docs/research-claude
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   research_claude.md
```

`Changes to be committed:` に表示されていれば、ステージに正しく追加されています。

> **確認のクセをつける:**
> `git commit` の前に `git status` を見る習慣を持つと、意図しないファイルをコミットするミスを防げます。

### Step 3: コミットする（`git commit`）

```bash
git commit -m "docs: Claude の調査メモを追加"
```

実行結果の例:

```
[docs/research-claude f3a9e12] docs: Claude の調査メモを追加
 1 file changed, 28 insertions(+)
 create mode 100644 research_claude.md
```

### Conventional Commits とは

コミットメッセージには **Conventional Commits** という書き方を使います。
「なんの変更か」がひと目でわかるように、prefix（前置詞）を付けます。

| prefix | 用途 | 例 |
|--------|------|----|
| `feat:` | 新機能・新コンテンツの追加 | `feat: 検索機能を追加` |
| `fix:` | バグ・誤字・誤記の修正 | `fix: setup.md の誤字を修正` |
| `docs:` | ドキュメントの追加・更新 | `docs: Claude の調査メモを追加` |
| `refactor:` | 動作を変えずに構成・記述を整理 | `refactor: 章の順序を整理` |

**良いコミットメッセージの条件:**

- 「何をしたか」だけでなく「なぜしたか」が伝わる
- 現在形・命令形で書く（「追加する」より「追加」）
- 50文字以内に収める（日本語で20〜30文字程度）

**悪い例:**

```
git commit -m "修正"          # 何を修正したか不明
git commit -m "update"        # 具体性がない
git commit -m "aaaa"          # 意味不明
```

**良い例:**

```
git commit -m "docs: Claude の調査メモを追加"
git commit -m "fix: research_claude.md の参考リンク URL を修正"
git commit -m "feat: 調査テンプレート research_template.md を追加"
```

---

## 6. GitHub に push する

### なぜ push が必要か

`git commit` はローカルリポジトリに変更を記録するだけです。
GitHub（リモートリポジトリ）に反映するには、**push** が必要です。

```
ローカル ──commit──▶ ローカルの履歴
ローカル ──push───▶  GitHub（リモート）の履歴
```

### 初回 push のコマンド

作業ブランチを初めて push するときは `-u` オプションを使います。

```bash
git push -u origin docs/research-claude
```

実行結果の例:

```
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 612 bytes | 612.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (0/0), done.
To git@github.com:kuro-daei/my-research-notes.git
 * [new branch]      docs/research-claude -> docs/research-claude
Branch 'docs/research-claude' set up to track remote branch 'docs/research-claude' from 'origin'.
```

> **`-u` オプションの意味:**
> `-u`（`--set-upstream`）は、ローカルブランチとリモートブランチを紐付けます。
> 一度設定すると、次回からは `git push` だけで同じリモートブランチに push できます。

> **`origin` とは:**
> clone 時に自動で設定される、リモートリポジトリの「ニックネーム」です。
> `git remote -v` で確認できます。

### 2回目以降の push

```bash
git push
```

### push 後の確認

ブラウザで GitHub のリポジトリページを開き、ブランチが届いているか確認します。
ページ上部に「Compare & pull request」というバナーが表示されれば成功です。

---

## 7. Pull Request を作成する

### Pull Request とは何か

**Pull Request（PR）** は、「このブランチの変更を main にマージしてください」という提案です。
チームで作業するときは、PR を通じてコードレビューを受けてからマージします。
個人作業でも PR を使う習慣を持つと、変更の記録が残り、ふりかえりがしやすくなります。

```
docs/research-claude ──────────▶ PR を作成 ──────────▶ main にマージ
         (作業ブランチ)                                    (本流)
```

### `gh pr create` で PR を作成する

```bash
gh pr create \
  --title "docs: Claude の調査メモを追加" \
  --body "## 変更内容

- research_claude.md を新規作成
- Claude の概要・特徴・参考リンクをまとめた

## 確認事項

- [ ] Markdown のプレビューを確認した
- [ ] リンクが正しく機能することを確認した"
```

実行結果の例:

```
Creating pull request for docs/research-claude into main in kuro-daei/my-research-notes

https://github.com/kuro-daei/my-research-notes/pull/1
```

### PR の書き方

良い PR には以下の要素が含まれます。

**タイトルの書き方:**

- Conventional Commits の prefix を使う（`docs:`, `feat:` など）
- 変更内容を簡潔に（50文字以内）
- 例: `docs: Claude の調査メモを追加`

**本文（body）の書き方:**

```markdown
## 変更内容

- 何を追加・変更したかを箇条書きで書く
- なぜその変更をしたかも書くと良い

## 確認事項

- [ ] 動作確認済み
- [ ] レビュー観点を記載した
```

> **Web UI から作成する場合:**
> push 後に GitHub のリポジトリページを開くと「Compare & pull request」ボタンが表示されます。
> それをクリックすると PR 作成画面が開きます。

### PR を確認する

```bash
gh pr list
```

出力例:

```
ID  TITLE                              BRANCH                 CREATED AT
1   docs: Claude の調査メモを追加      docs/research-claude   2026-03-08
```

PR の詳細を見る:

```bash
gh pr view 1
```

---

## 8. PR をマージして GitHub の中身が変わったことを確認する

### PR をマージする

```bash
gh pr merge 1 --merge
```

実行結果の例:

```
✓ Merged pull request #1 (docs: Claude の調査メモを追加)
```

> **マージ方法の種類:**
>
> | オプション | 意味 | 使い所 |
> |------------|------|--------|
> | `--merge` | 通常マージ（マージコミットを作成） | 履歴を残したいとき |
> | `--squash` | スカッシュマージ（複数コミットを1つに圧縮） | コミットを整理したいとき |
> | `--rebase` | リベースマージ（線形な履歴を保つ） | 履歴を綺麗に保ちたいとき |
>
> 学習段階では `--merge` を使えば問題ありません。

### ブラウザで確認する

マージ後、GitHub のリポジトリページを開きます。

1. `main` ブランチが選択されていることを確認
2. `research_claude.md` が表示されていれば成功
3. ファイルをクリックすると内容が表示される

また、「Commits」タブをクリックすると、コミット履歴に今回の変更が記録されているのを確認できます。

### ローカルの main を最新にする（`git pull`）

マージは GitHub（リモート）で行われたため、ローカルの `main` はまだ古い状態です。
`git pull` でリモートの変更をローカルに取り込みます。

```bash
git checkout main
git pull
```

実行結果の例:

```
Switched to branch 'main'
Your branch is behind 'origin/main' by 2 commits, and can be updated.
  (use "git pull" to update your local branch)
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Updating a1b2c3d..f3a9e12
Fast-forward
 research_claude.md | 28 ++++++++++++++++++++++++++++
 1 file changed, 28 insertions(+)
 create mode 100644 research_claude.md
```

ローカルで確認:

```bash
ls
```

出力例:

```
README.md  research_claude.md
```

`research_claude.md` が `main` に存在すれば、すべての手順が完了です。

---

## 9. 後片付け（ブランチ削除）

### なぜブランチを削除するのか

マージが完了したブランチは役割を終えています。
不要なブランチを残したままにすると、リポジトリが散らかり、管理が難しくなります。
作業ごとにブランチを作っては削除する習慣をつけましょう。

### リモートブランチを削除する

```bash
git push origin --delete docs/research-claude
```

実行結果の例:

```
To git@github.com:kuro-daei/my-research-notes.git
 - [deleted]         docs/research-claude
```

または `gh` を使う:

```bash
gh api repos/kuro-daei/my-research-notes/git/refs/heads/docs/research-claude \
  --method DELETE
```

> **PR マージ時に自動削除する設定:**
> GitHub のリポジトリ設定（Settings → General）で「Automatically delete head branches」を有効にすると、
> PR マージ後にリモートブランチが自動で削除されます。

### ローカルブランチを削除する

```bash
git branch -d docs/research-claude
```

実行結果の例:

```
Deleted branch docs/research-claude (was f3a9e12).
```

> **`-d` と `-D` の違い:**
> `-d` は「マージ済みのブランチのみ」削除します。未マージのブランチを削除しようとするとエラーになります。
> `-D` は強制削除です。未マージでも削除できますが、変更が失われる危険があります。

### 最終確認

```bash
git branch
```

出力例:

```
* main
```

`main` だけが残っていれば、後片付け完了です。

---

## 10. まとめ

### ワークフロー全体図（コマンド付き）

```
┌──────────────────────────────────────────────────────────────────┐
│             GitHub 公開ワークフロー（コマンド早見表）                 │
└──────────────────────────────────────────────────────────────────┘

1. リポジトリ作成
   gh repo create my-research-notes --public --add-readme

2. クローン
   git clone git@github.com:ユーザ名/my-research-notes.git
   cd my-research-notes

3. ブランチ作成
   git checkout -b docs/research-xxx

4. ファイル作成・編集
   code .        ← VS Code で編集

5. commit
   git add ファイル名
   git status    ← 確認
   git commit -m "docs: 調査メモを追加"

6. push
   git push -u origin docs/research-xxx

7. PR 作成
   gh pr create --title "docs: 調査メモを追加" --body "..."

8. マージ
   gh pr merge 番号 --merge
   git checkout main
   git pull

9. 後片付け
   git push origin --delete docs/research-xxx
   git branch -d docs/research-xxx
```

### 今回学んだこと

| ステップ | コマンド | 役割 |
|----------|----------|------|
| リポジトリ作成 | `gh repo create` | GitHub 上にリポジトリを用意する |
| クローン | `git clone` | リモートをローカルにコピーする |
| ブランチ作成 | `git checkout -b` | 安全な作業スペースを作る |
| ステージング | `git add` | コミット対象ファイルを指定する |
| コミット | `git commit -m` | 変更を履歴に記録する |
| プッシュ | `git push` | ローカルの変更をリモートに送る |
| PR 作成 | `gh pr create` | マージの提案をする |
| マージ | `gh pr merge` | ブランチを main に統合する |
| 同期 | `git pull` | リモートの変更をローカルに取り込む |
| 後片付け | `git branch -d` | 不要なブランチを削除する |

### よくあるエラーと対処法

| エラーメッセージ | 原因 | 対処法 |
|----------------|------|--------|
| `Permission denied (publickey)` | SSH 鍵が未設定 | `ssh-keygen` で鍵を作成し GitHub に登録 |
| `error: failed to push some refs` | リモートが先行している | `git pull` してからもう一度 `git push` |
| `fatal: not a git repository` | Git リポジトリ外で操作した | `cd` でリポジトリ内に移動してから実行 |
| `Your branch is behind 'origin/main'` | ローカルが古い | `git pull` で最新を取得 |
| `nothing to commit` | 変更がステージされていない | `git add` でファイルを追加してから `git commit` |

---

## 次のステップ

ここまでで、調査メモを GitHub に公開する完全なワークフローを体験しました。
次の章では、クローンしたリポジトリの中で Claude Code を使う方法を学びます。

[02_claude_usage.md](./02_claude_usage.md) — Claude Code の使い方
