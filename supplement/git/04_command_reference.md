# 補足 06. Git・GitHub コマンドリファレンス早見表

作業中に手元で参照するためのリファレンスです。よく使うコマンドを目的別に整理しています。

---

## 目次

1. [Bash 基本コマンド](#1-bash-基本コマンド)
2. [Git 基本操作](#2-git-基本操作)
   - [リポジトリ初期化・クローン](#21-リポジトリ初期化クローン)
   - [状態確認](#22-状態確認)
   - [ステージング](#23-ステージング)
   - [コミット](#24-コミット)
   - [ブランチ操作](#25-ブランチ操作)
   - [リモート操作](#26-リモート操作)
   - [マージ・リベース](#27-マージリベース)
   - [その他便利コマンド](#28-その他便利コマンド)
3. [gh CLI コマンド](#3-gh-cli-コマンド)
   - [認証](#31-認証)
   - [リポジトリ操作](#32-リポジトリ操作)
   - [PR 操作](#33-pr-操作)
   - [Issue 操作](#34-issue-操作)
4. [よく使うコマンドの組み合わせ（シナリオ別）](#4-よく使うコマンドの組み合わせシナリオ別)
   - [新規作業開始フロー](#41-新規作業開始フロー)
   - [PR 作成フロー](#42-pr-作成フロー)
   - [コンフリクト解決フロー](#43-コンフリクト解決フロー)

---

## 1. Bash 基本コマンド

| コマンド | 説明 | 使用例 |
|---|---|---|
| `pwd` | 現在のディレクトリの絶対パスを表示する | `pwd` |
| `cd` | ディレクトリを移動する | `cd ~/projects/myapp` |
| `cd ..` | 1つ上のディレクトリへ移動する | `cd ..` |
| `cd -` | 直前にいたディレクトリへ戻る | `cd -` |
| `ls` | ディレクトリの内容を一覧表示する | `ls` |
| `ls -l` | 詳細情報（権限・サイズ・日時）付きで一覧表示する | `ls -l` |
| `ls -a` | 隠しファイル（`.`で始まるファイル）も含めて表示する | `ls -a` |
| `ls -la` | 詳細情報＋隠しファイルを表示する | `ls -la` |
| `mkdir` | 新しいディレクトリを作成する | `mkdir docs` |
| `mkdir -p` | 中間ディレクトリも含めて再帰的に作成する | `mkdir -p src/components/ui` |
| `touch` | 空のファイルを作成する（既存ファイルのタイムスタンプを更新する） | `touch README.md` |
| `cat` | ファイルの内容を標準出力に表示する | `cat README.md` |
| `echo` | 文字列を標準出力に表示する | `echo "Hello, World!"` |
| `echo >` | 文字列をファイルに書き込む（上書き） | `echo "# title" > README.md` |
| `echo >>` | 文字列をファイルに追記する | `echo "line" >> README.md` |
| `rm` | ファイルを削除する | `rm old_file.txt` |
| `rm -r` | ディレクトリごと再帰的に削除する | `rm -r old_dir/` |
| `rm -rf` | 確認なしで強制的に削除する（注意して使用すること） | `rm -rf dist/` |
| `cp` | ファイルをコピーする | `cp file.txt file_backup.txt` |
| `mv` | ファイルを移動する・名前を変更する | `mv old.md new.md` |
| `less` | ファイルをページ単位で閲覧する（`q` で終了） | `less README.md` |

---

## 2. Git 基本操作

### 2.1 リポジトリ初期化・クローン

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git init` | 現在のディレクトリを Git リポジトリとして初期化する | `git init` |
| `git clone <URL>` | リモートリポジトリをローカルにコピーする | `git clone git@github.com:user/repo.git` |
| `git clone <URL> <dir>` | 指定したディレクトリ名でクローンする | `git clone git@github.com:user/repo.git myapp` |

---

### 2.2 状態確認

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git status` | 作業ツリーとステージングエリアの状態を確認する | `git status` |
| `git status -s` | 短縮形式で状態を表示する | `git status -s` |
| `git diff` | 作業ツリーとステージングエリアの差分を表示する | `git diff` |
| `git diff --staged` | ステージング済みの差分（コミットとの差分）を表示する | `git diff --staged` |
| `git diff <branch>` | 現在のブランチと指定ブランチの差分を表示する | `git diff main` |
| `git log` | コミット履歴を表示する | `git log` |
| `git log --oneline` | 1行形式でコミット履歴を表示する | `git log --oneline` |
| `git log --oneline --graph` | ブランチの分岐をグラフ形式で表示する | `git log --oneline --graph --all` |
| `git log -n <数>` | 最新の指定件数だけ表示する | `git log -n 5` |
| `git show <commit>` | 指定したコミットの詳細と差分を表示する | `git show abc1234` |

---

### 2.3 ステージング

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git add <file>` | 指定したファイルをステージングエリアに追加する | `git add README.md` |
| `git add .` | 現在のディレクトリ以下の変更をすべてステージングする | `git add .` |
| `git add -p` | 変更を対話的に選択してステージングする | `git add -p` |
| `git restore --staged <file>` | ステージングを取り消す（ファイルの変更は保持される） | `git restore --staged README.md` |
| `git rm <file>` | ファイルを削除してその変更をステージングする | `git rm old_file.txt` |
| `git rm --cached <file>` | Git の追跡からのみ外す（ファイルは残す） | `git rm --cached secret.env` |

---

### 2.4 コミット

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git commit -m "<message>"` | ステージングした変更をコミットする | `git commit -m "feat: ログイン機能を追加"` |
| `git commit -am "<message>"` | 追跡済みファイルをステージングしてコミットする | `git commit -am "fix: タイポを修正"` |
| `git commit --amend` | 直前のコミットを修正する（メッセージ・内容） | `git commit --amend` |
| `git commit --amend -m "<message>"` | 直前のコミットのメッセージのみ変更する | `git commit --amend -m "fix: 正しいメッセージ"` |

**コミットメッセージの形式（Conventional Commits）**

| prefix | 用途 |
|---|---|
| `feat:` | 新機能・新コンテンツ追加 |
| `fix:` | バグ修正・誤字修正 |
| `docs:` | ドキュメントの更新 |
| `refactor:` | 機能変更を伴わないコード・構成の整理 |
| `test:` | テストの追加・修正 |
| `chore:` | ビルドや設定ファイルの変更 |

---

### 2.5 ブランチ操作

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git branch` | ローカルブランチの一覧を表示する | `git branch` |
| `git branch -a` | リモートブランチも含めて一覧表示する | `git branch -a` |
| `git branch <name>` | 新しいブランチを作成する（移動はしない） | `git branch feature/login` |
| `git branch -d <name>` | ブランチを削除する（マージ済みのみ） | `git branch -d feature/login` |
| `git branch -D <name>` | ブランチを強制削除する（未マージでも削除） | `git branch -D feature/wip` |
| `git branch -m <old> <new>` | ブランチ名を変更する | `git branch -m feature/old feature/new` |
| `git switch <branch>` | 指定したブランチに切り替える | `git switch main` |
| `git switch -c <name>` | ブランチを作成して切り替える | `git switch -c feature/login` |
| `git switch --detach <hash>` | 特定のコミットに切り替える（detached HEAD） | `git switch --detach a1b2c3d` |
| `git checkout <branch>` | 旧記法（非推奨） | `git checkout main` |

---

### 2.6 リモート操作

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git remote -v` | リモートリポジトリの一覧と URL を確認する | `git remote -v` |
| `git remote add <name> <URL>` | リモートリポジトリを追加する | `git remote add origin git@github.com:user/repo.git` |
| `git remote remove <name>` | リモートリポジトリの設定を削除する | `git remote remove origin` |
| `git push` | 現在のブランチをリモートにプッシュする | `git push` |
| `git push -u origin <branch>` | 初回プッシュ時に追跡ブランチを設定する | `git push -u origin feature/login` |
| `git push origin --delete <branch>` | リモートブランチを削除する | `git push origin --delete feature/login` |
| `git pull` | リモートの変更を取得してマージする | `git pull` |
| `git pull --rebase` | リモートの変更を取得してリベースする | `git pull --rebase` |
| `git fetch` | リモートの変更情報だけ取得する（マージはしない） | `git fetch` |
| `git fetch --prune` | 削除されたリモートブランチの追跡情報も削除する | `git fetch --prune` |

---

### 2.7 マージ・リベース

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git merge <branch>` | 指定したブランチを現在のブランチにマージする | `git merge feature/login` |
| `git merge --no-ff <branch>` | Fast-forward しないでマージコミットを作成する | `git merge --no-ff feature/login` |
| `git merge --abort` | マージを中断して元の状態に戻す | `git merge --abort` |
| `git rebase <branch>` | 現在のブランチを指定ブランチ上にリベースする | `git rebase main` |
| `git rebase --continue` | コンフリクト解決後にリベースを再開する | `git rebase --continue` |
| `git rebase --abort` | リベースを中断して元の状態に戻す | `git rebase --abort` |
| `git cherry-pick <commit>` | 指定したコミットを現在のブランチに適用する | `git cherry-pick abc1234` |

---

### 2.8 その他便利コマンド

| コマンド | 説明 | 使用例 |
|---|---|---|
| `git stash` | 作業中の変更を一時保存する | `git stash` |
| `git stash push -m "<message>"` | メッセージ付きで変更を一時保存する | `git stash push -m "ログイン機能WIP"` |
| `git stash list` | スタッシュの一覧を表示する | `git stash list` |
| `git stash pop` | 最新のスタッシュを取り出す（スタッシュから削除） | `git stash pop` |
| `git stash apply` | 最新のスタッシュを取り出す（スタッシュを保持） | `git stash apply` |
| `git stash drop` | 最新のスタッシュを削除する | `git stash drop` |
| `git stash clear` | すべてのスタッシュを削除する | `git stash clear` |
| `git tag <name>` | 現在のコミットに軽量タグをつける | `git tag v1.0.0` |
| `git tag -a <name> -m "<message>"` | 注釈付きタグをつける | `git tag -a v1.0.0 -m "リリース v1.0.0"` |
| `git tag` | タグの一覧を表示する | `git tag` |
| `git push origin <tag>` | タグをリモートにプッシュする | `git push origin v1.0.0` |
| `git push origin --tags` | すべてのタグをプッシュする | `git push origin --tags` |
| `git reset HEAD~1` | 直前のコミットを取り消す（変更は作業ツリーに残す） | `git reset HEAD~1` |
| `git reset --hard HEAD~1` | 直前のコミットを取り消す（変更も破棄する） | `git reset --hard HEAD~1` |
| `git restore <file>` | 作業ツリーのファイルを直前のコミット状態に戻す | `git restore README.md` |

---

## 3. gh CLI コマンド

> `gh` は GitHub 公式の CLI ツールです。ブラウザを開かずに PR・Issue の操作ができます。

### 3.1 認証

| コマンド | 説明 | 使用例 |
|---|---|---|
| `gh auth login` | GitHub アカウントで認証する | `gh auth login` |
| `gh auth logout` | 認証を解除する | `gh auth logout` |
| `gh auth status` | 現在の認証状態を確認する | `gh auth status` |
| `gh auth token` | 現在使用しているトークンを表示する | `gh auth token` |

---

### 3.2 リポジトリ操作

| コマンド | 説明 | 使用例 |
|---|---|---|
| `gh repo create` | 対話形式でリポジトリを作成する | `gh repo create` |
| `gh repo create <name> --public` | パブリックリポジトリを作成する | `gh repo create myapp --public` |
| `gh repo create <name> --private` | プライベートリポジトリを作成する | `gh repo create myapp --private` |
| `gh repo clone <repo>` | リポジトリをクローンする | `gh repo clone user/myapp` |
| `gh repo view` | 現在のリポジトリの概要を表示する | `gh repo view` |
| `gh repo view --web` | ブラウザでリポジトリを開く | `gh repo view --web` |
| `gh repo fork` | リポジトリをフォークする | `gh repo fork user/oss-project` |
| `gh repo list` | 自分のリポジトリ一覧を表示する | `gh repo list` |

---

### 3.3 PR 操作

| コマンド | 説明 | 使用例 |
|---|---|---|
| `gh pr create` | 対話形式で PR を作成する | `gh pr create` |
| `gh pr create --title "<title>" --body "<body>"` | タイトルと本文を指定して PR を作成する | `gh pr create --title "feat: ログイン機能" --body "説明"` |
| `gh pr create --draft` | ドラフト PR として作成する | `gh pr create --draft` |
| `gh pr list` | オープン中の PR 一覧を表示する | `gh pr list` |
| `gh pr list --state closed` | クローズ済みの PR を表示する | `gh pr list --state closed` |
| `gh pr view` | 現在のブランチに対応する PR を表示する | `gh pr view` |
| `gh pr view <number>` | 指定した番号の PR を表示する | `gh pr view 42` |
| `gh pr view --web` | ブラウザで PR を開く | `gh pr view --web` |
| `gh pr checkout <number>` | PR のブランチをローカルにチェックアウトする | `gh pr checkout 42` |
| `gh pr merge <number>` | PR をマージする | `gh pr merge 42` |
| `gh pr merge <number> --squash` | スカッシュマージする | `gh pr merge 42 --squash` |
| `gh pr merge <number> --rebase` | リベースマージする | `gh pr merge 42 --rebase` |
| `gh pr diff <number>` | PR の差分を表示する | `gh pr diff 42` |
| `gh pr review <number> --approve` | PR を承認する | `gh pr review 42 --approve` |
| `gh pr review <number> --request-changes` | 変更を要求する | `gh pr review 42 --request-changes -b "コメント"` |
| `gh pr close <number>` | PR をクローズする | `gh pr close 42` |

---

### 3.4 Issue 操作

| コマンド | 説明 | 使用例 |
|---|---|---|
| `gh issue create` | 対話形式で Issue を作成する | `gh issue create` |
| `gh issue create --title "<title>" --body "<body>"` | タイトルと本文を指定して Issue を作成する | `gh issue create --title "バグ報告" --body "詳細"` |
| `gh issue list` | オープン中の Issue 一覧を表示する | `gh issue list` |
| `gh issue list --state closed` | クローズ済みの Issue を表示する | `gh issue list --state closed` |
| `gh issue list --label "<label>"` | ラベルで絞り込んで表示する | `gh issue list --label "bug"` |
| `gh issue view <number>` | 指定した番号の Issue を表示する | `gh issue view 10` |
| `gh issue view --web` | ブラウザで Issue を開く | `gh issue view 10 --web` |
| `gh issue close <number>` | Issue をクローズする | `gh issue close 10` |
| `gh issue reopen <number>` | Issue を再オープンする | `gh issue reopen 10` |
| `gh issue comment <number> -b "<comment>"` | Issue にコメントを追加する | `gh issue comment 10 -b "確認しました"` |

---

## 4. よく使うコマンドの組み合わせ（シナリオ別）

### 4.1 新規作業開始フロー

新しい機能や修正を始めるときの基本フローです。

```bash
# 1. メインブランチを最新状態に更新する
git switch main
git pull

# 2. 作業用ブランチを作成して切り替える
git switch -c feature/add-login

# 3. ファイルを編集する（エディタで作業）

# 4. 変更内容を確認する
git status
git diff

# 5. 変更をステージングする
git add src/login.md

# 6. コミットする
git commit -m "feat: ログイン機能の説明を追加"

# 7. 必要に応じて繰り返す（3〜6 を繰り返す）
```

---

### 4.2 PR 作成フロー

作業が完了してプルリクエストを作成するときのフローです。

```bash
# 1. 最新のメインブランチの変更を取り込む
git switch main
git pull
git switch feature/add-login

# 2. メインブランチの変更をリベースで取り込む（コンフリクトがあれば解決する）
git rebase main

# 3. リモートにプッシュする（初回は -u フラグが必要）
git push -u origin feature/add-login

# 4. PR を作成する
gh pr create --title "feat: ログイン機能の説明を追加" --body "## 概要
- ログイン手順のドキュメントを追加しました"

# 5. PR の URL が表示されるのでブラウザで確認する
# または
gh pr view --web

# 6. レビュー後、マージする
gh pr merge 42 --squash

# 7. ローカルのブランチを削除する
git switch main
git pull
git branch -d feature/add-login
```

---

### 4.3 コンフリクト解決フロー

マージやリベース中にコンフリクトが発生したときのフローです。

```bash
# [状況] git rebase main や git merge main でコンフリクトが発生した場合

# 1. コンフリクトしているファイルを確認する
git status
# "both modified: src/README.md" のように表示される

# 2. エディタでコンフリクトマーカーを手動で解決する
# <<<<<<< HEAD
# 現在のブランチの内容
# =======
# マージ元の内容
# >>>>>>> main
# 上記のマーカーを削除して、正しい内容に書き換える

# 3. 解決したファイルをステージングする
git add src/README.md

# 4a. リベース中の場合: 続行する
git rebase --continue

# 4b. マージ中の場合: コミットする
git commit -m "fix: コンフリクトを解決"

# --- 途中でやめたい場合 ---

# リベースを中断する（元の状態に戻る）
git rebase --abort

# マージを中断する（元の状態に戻る）
git merge --abort
```

---

## 付録：よく使うオプション一覧

### `git log` の表示カスタマイズ

```bash
# コンパクトなグラフ表示
git log --oneline --graph --all --decorate

# 日付でフィルタリング
git log --since="2024-01-01" --until="2024-12-31"

# 特定のファイルの変更履歴
git log --follow -- README.md

# 特定のキーワードを含むコミット
git log --grep="feat"
```

### `.gitignore` のよくある記述

```gitignore
# OS・エディタ生成ファイル
.DS_Store
Thumbs.db
.vscode/

# 環境変数・シークレット
.env
.env.local
*.key

# ビルド成果物・依存関係
node_modules/
dist/
*.log
```

---

*このリファレンスは `01_bash_basics.md` から `09_pull_request.md` の本編、および補足資料の内容を補完する付録です。詳しい解説は各章を参照してください。*

[補足資料一覧に戻る](../../10_supplement_index.md)
