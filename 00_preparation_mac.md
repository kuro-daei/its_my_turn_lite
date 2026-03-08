# 00. 事前準備（macOS 版）— 開発環境のセットアップ

> **Windows をお使いの方へ:** [00_preparation_windows.md](./00_preparation_windows.md) を参照してください。

この章では、以降の学習で使うすべてのツールをインストールします。
手順通りに進めれば、Git・GitHub・Claude Code が使える状態になります。

---

## 1. この章でやること

以下の順序でツールをセットアップします。
依存関係があるため、**必ずこの順番で実施してください**。

```
+------------------+
|      macOS       |
+--------+---------+
         |
         v
+------------------+     /bin/bash -c "$(curl -fsSL ...)" でインストール
|    Homebrew      |   （macOS 用パッケージマネージャー）
+--------+---------+
         |
         v
+------------------+     brew install --cask visual-studio-code
|    VS Code       |
+--------+---------+
         |
         v
+------------------+     brew install git
|      Git         |
+--------+---------+
         |
         v
+------------------+     ssh-keygen → GitHub に公開鍵を登録
|  GitHub (SSH)    |
+--------+---------+
         |
         v
+------------------+     brew install gh
|    gh CLI        |
+--------+---------+
         |
         v
+------------------+     curl インストーラーでインストール
|  Claude Code     |
+--------+---------+
         |
         v
+------------------+     api-dashboard.search.brave.com でキー取得
|  Brave Search    |
|  API キー        |
+------------------+
```

各ステップの完了確認コマンドを用意しています。
コマンドを実行して期待通りの出力が得られたら、次のステップに進んでください。

---

## 2. 前提条件

作業を始める前に、以下を確認してください。

### 必須要件

| 項目 | 要件 | 確認方法 |
|---|---|---|
| OS | macOS 12 Monterey 以上（推奨: 13 Ventura / 14 Sonoma / 15 Sequoia） | アップルメニュー → 「このMacについて」 |
| インターネット接続 | 有線・無線どちらでも可 | ブラウザで任意のサイトを開いて確認 |
| GitHub アカウント | 無料アカウントで可 | [github.com](https://github.com) でログイン確認 |
| Claude アカウント | Pro / Max / Teams / Enterprise / Console のいずれか（**無料プランは不可**） | [claude.ai](https://claude.ai) でプランを確認 |
| ディスク空き容量 | 5 GB 以上を推奨 | アップルメニュー → 「このMacについて」→「ストレージ」 |

### macOS のバージョンを確認する

アップルメニュー（画面左上の）から「このMacについて」を選択するとバージョンが確認できます。

または、ターミナルから確認することもできます。

```bash
sw_vers
```

期待される出力例：

```
ProductName:    macOS
ProductVersion: 14.4.1
BuildVersion:   23E224
```

`ProductVersion` が 12.0 以上であれば問題ありません。
もし 12 未満の場合は、システム設定からソフトウェアアップデートを行ってください。

### Apple Silicon (M系チップ) か Intel Mac か確認する

後の手順で**チップの種類によって設定が異なる**ため、事前に確認しておきます。

```bash
uname -m
```

| 出力 | 意味 |
|---|---|
| `arm64` | Apple Silicon（M1/M2/M3/M4 など） |
| `x86_64` | Intel Mac |

> **Apple Silicon とは？** 2020年後半以降に発売された Mac に搭載されている Apple 独自設計のチップです（M1, M2, M3, M4 シリーズ）。Intel Mac とはアーキテクチャが異なるため、一部のツールで設定方法が違います。

---

## 3. ターミナルの起動

ターミナル（Terminal）は、コマンドを入力して Mac を操作するアプリケーションです。
これ以降の作業はすべてターミナルで行います。

### 3-1. ターミナルを起動する

**方法 1: Spotlight 検索から起動する（推奨）**

1. `Cmd + Space` を押して Spotlight 検索を開く
2. 「ターミナル」または「terminal」と入力する
3. 候補に「ターミナル」が表示されたら `Enter` を押す

**方法 2: Launchpad から起動する**

1. Dock の Launchpad アイコン（ロケットのアイコン）をクリック
2. 検索ボックスに「ターミナル」と入力
3. 「ターミナル」アイコンをクリック

**方法 3: Finder から起動する**

1. Finder を開く
2. 「アプリケーション」→「ユーティリティ」フォルダを開く
3. 「ターミナル」をダブルクリック

### 3-2. デフォルトシェルを確認する

macOS 10.15 Catalina 以降、デフォルトのシェルは **zsh**（ズィーシェル）です。

```bash
echo $SHELL
```

期待される出力：

```
/bin/zsh
```

> **zsh とは？** コマンドを解釈して実行するプログラムです。以前の macOS では bash がデフォルトでしたが、Catalina 以降は zsh が標準です。この教材では zsh を前提として説明します。

---

## 4. Homebrew のインストール

Homebrew は macOS 向けのパッケージマネージャーです。
ソフトウェアのインストール・更新・削除を簡単なコマンドで行えます。
「Mac の apt」と考えると分かりやすいでしょう。

### 4-1. Homebrew をインストールする

ターミナルで以下のコマンドを実行します（1行全体をコピーして貼り付けてください）。

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

インストール中に以下の確認が表示されます。

```
==> The following new directories will be created:
/opt/homebrew/bin
...
Press RETURN/ENTER to continue or any other key to abort:
```

`Enter` を押して続行します。

続けてシステムパスワードの入力を求められます。

```
==> Checking for `sudo` access (which may request your password)...
Password:
```

Mac のログインパスワードを入力してください（入力中は画面に何も表示されません）。

インストールには数分かかります。以下のような出力が続きます。

```
==> Installing Homebrew...
==> Downloading and installing Homebrew...
...
==> Installation successful!
```

> **注意:** インストール中に Xcode Command Line Tools のインストールも自動的に行われる場合があります。その場合は、追加で数分かかります。

### 4-2. Apple Silicon Mac の PATH 設定（重要）

**Apple Silicon（M系チップ）の Mac のみ**、追加の PATH 設定が必要です。

インストール完了後に、ターミナルに以下のような指示が表示されます。

```
==> Next steps:
- Run these commands in your terminal to add Homebrew to your PATH:
    echo >> /Users/username/.zprofile
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/username/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
```

**表示された通りのコマンドを実行してください。** 一般的には以下の2つのコマンドです。

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

| チップ | Homebrew のインストール先 | PATH 設定 |
|---|---|---|
| Apple Silicon（M系） | `/opt/homebrew/` | 上記の設定が必要 |
| Intel | `/usr/local/` | 自動設定されるため通常不要 |

> **なぜ Apple Silicon だけ設定が必要？** Apple Silicon 向けの Homebrew は `/opt/homebrew/` にインストールされますが、このディレクトリはデフォルトの PATH に含まれていません。`~/.zprofile` に追記することで、新しいターミナルを開くたびに自動的に PATH が設定されるようになります。

### 4-3. バージョンを確認する

```bash
brew --version
```

期待される出力例：

```
Homebrew 4.x.x
```

バージョン番号が表示されれば、インストール成功です。

---

## 5. VS Code のインストール

VS Code（Visual Studio Code）はテキストエディタです。
コードを書いたり、Markdown ファイルをプレビューしたりするために使います。

### 5-1. Homebrew で VS Code をインストールする（推奨）

```bash
brew install --cask visual-studio-code
```

実行結果の例：

```
==> Downloading https://update.code.visualstudio.com/...
==> Installing Cask visual-studio-code
==> Moving App 'Visual Studio Code.app' to '/Applications/Visual Studio Code.app'
visual-studio-code was successfully installed!
```

> **Cask とは？** Homebrew の「cask」は GUI アプリケーション（.app 形式のファイル）をインストールするための仕組みです。コマンドラインツールは `brew install`、GUI アプリは `brew install --cask` を使います。

**または、公式サイトからダウンロードする場合：**

1. ブラウザで [https://code.visualstudio.com](https://code.visualstudio.com) を開く
2. 「Download Mac Universal」をクリック（M系チップと Intel の両方に対応）
3. ダウンロードされた `.zip` ファイルを解凍する
4. 展開された「Visual Studio Code.app」を「アプリケーション」フォルダに移動する

### 5-2. `code` コマンドをセットアップする

ターミナルから `code` コマンドで VS Code を起動できるように設定します。

1. VS Code を起動する（Spotlight 検索や Launchpad から）
2. `Cmd + Shift + P` を押してコマンドパレットを開く
3. 「shell command」と入力する
4. 「Shell Command: Install 'code' command in PATH」をクリックする

```
Shell command 'code' successfully installed in PATH.
```

と表示されれば設定完了です。

> **注意:** Homebrew でインストールした場合、この設定は自動で行われている場合もあります。

### 5-3. バージョンを確認する

ターミナルを再起動（または新しいタブを開く）した後、以下を実行します。

```bash
code --version
```

期待される出力例：

```
1.97.x
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
x64
```

バージョン番号が表示されれば、インストール成功です。

---

## 6. Git のインストールと初期設定

Git はバージョン管理ツールです。ファイルの変更履歴を記録し、チームで共同作業するために使います。

### macOS の Git について

macOS には Git がプリインストールされていますが、古いバージョン（多くの場合 2.x の古いリリース）が入っています。
Homebrew で最新版をインストールし、常に新しい機能とセキュリティ修正を利用できるようにします。

### 6-1. Homebrew で Git をインストールする

```bash
brew install git
```

実行結果の例：

```
==> Downloading https://ghcr.io/v2/homebrew/core/git/...
==> Installing git
...
==> Summary
🍺  /opt/homebrew/Cellar/git/2.x.x: xxx files, xx.xMB
```

### 6-2. バージョンを確認する

```bash
git --version
```

期待される出力例：

```
git version 2.47.x
```

> **注意:** `which git` を実行したとき、`/opt/homebrew/bin/git`（Apple Silicon）または `/usr/local/bin/git`（Intel）が表示されれば、Homebrew 版の Git が使われています。`/usr/bin/git` と表示された場合は、macOS プリインストール版が使われています。PATH の設定を確認してください。

### 6-3. ユーザー情報を設定する

Git のコミット（変更の記録）に名前とメールアドレスが紐付けられます。
GitHub に登録したものと同じ情報を設定することを推奨します。

```bash
git config --global user.name "あなたの名前"
git config --global user.email "your-email@example.com"
```

> **注意:** `user.email` には GitHub に登録したメールアドレスを使ってください。
> 異なるメールアドレスを使うと、GitHub のコントリビューションが正しく記録されない場合があります。

### 6-4. デフォルトブランチ名を設定する

GitHub の既定ブランチ名は `main` です。Git 側も合わせておきます。

```bash
git config --global init.defaultBranch main
```

### 6-5. エディタを VS Code に設定する

コミットメッセージを編集するエディタを VS Code に設定します。

```bash
git config --global core.editor "code --wait"
```

### 6-6. 設定内容を確認する

```bash
git config --global --list
```

期待される出力例：

```
user.name=あなたの名前
user.email=your-email@example.com
init.defaultbranch=main
core.editor=code --wait
```

設定した内容が表示されれば完了です。

---

## 7. GitHub アカウントの準備と SSH 設定

GitHub へのアクセスには HTTPS と SSH の2つの方法があります。
ここでは**SSH**を使います。SSH は一度設定すれば、パスワード入力なしに GitHub を操作できます。

macOS では**Keychain（キーチェーン）**を使って SSH キーを安全に管理できます。
これは macOS 固有の便利な機能で、Mac を再起動してもパスフレーズを再入力する必要がありません。

### 7-1. SSH 鍵を生成する

ターミナルで以下を実行します。
`your-email@example.com` の部分は GitHub に登録したメールアドレスに変更してください。

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

実行中に以下の質問が表示されます。

```
Enter file in which to save the key (/Users/username/.ssh/id_ed25519):
```

そのまま `Enter` を押します（デフォルトの場所に保存されます）。

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

パスフレーズは設定することを推奨します。macOS の Keychain に登録するため、毎回入力する必要はありません。
空のままにする場合は `Enter` を2回押してください。

実行結果の例：

```
Your identification has been saved in /Users/username/.ssh/id_ed25519
Your public key has been saved in /Users/username/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your-email@example.com
The key's randomart image is:
+--[ED25519 256]--+
|       .o+o. .   |
...
```

### 7-2. SSH config ファイルを設定する（macOS Keychain 連携）

macOS の Keychain に SSH キーを登録するため、`~/.ssh/config` ファイルを作成・編集します。

まず、`.ssh` ディレクトリが存在するか確認します。

```bash
ls ~/.ssh/
```

次に、config ファイルを VS Code で開いて編集します（ファイルがなければ新規作成されます）。

```bash
code ~/.ssh/config
```

以下の内容を追記して保存します（`Cmd + S`）。

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

| 設定項目 | 意味 |
|---|---|
| `AddKeysToAgent yes` | SSH キーを ssh-agent に自動追加する |
| `UseKeychain yes` | macOS の Keychain にパスフレーズを保存する |
| `IdentityFile ~/.ssh/id_ed25519` | 使用する SSH 秘密鍵のパス |

> **UseKeychain とは？** macOS の Keychain Access（キーチェーンアクセス）は、パスワードや証明書を安全に管理するシステムです。SSH のパスフレーズを Keychain に登録することで、Mac の再起動後も自動的にパスフレーズが読み込まれます。

### 7-3. SSH キーを Keychain に登録する

以下のコマンドで SSH キーを macOS Keychain に登録します。

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

パスフレーズを設定した場合は入力を求められます。

```
Enter passphrase for /Users/username/.ssh/id_ed25519:
Identity added: /Users/username/.ssh/id_ed25519 (your-email@example.com)
```

> **`--apple-use-keychain` とは？** このオプションは macOS 専用で、SSH キーのパスフレーズを macOS の Keychain に保存します。次回から自動的に読み込まれるため、パスフレーズの再入力が不要になります。古い macOS では `-K` オプションを使う場合もありましたが、現在は `--apple-use-keychain` が推奨されています。

### 7-4. 公開鍵を表示してクリップボードにコピーする

macOS では `pbcopy` コマンドでコンテンツをクリップボードに直接コピーできます。

```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```

コマンドを実行しても画面には何も表示されませんが、クリップボードに公開鍵がコピーされています。

> **`pbcopy` とは？** macOS のクリップボードにテキストをコピーするコマンドです。`pbpaste` を使うとクリップボードの内容を表示できます。WSL/Linux の `xclip`・`xsel` に相当する macOS 固有のコマンドです。

確認のため、内容を表示する場合は以下を実行します。

```bash
cat ~/.ssh/id_ed25519.pub
```

出力例：

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your-email@example.com
```

この1行全体が公開鍵です（`ssh-ed25519` から始まりメールアドレスで終わります）。

### 7-5. GitHub に公開鍵を登録する

1. ブラウザで [https://github.com/settings/keys](https://github.com/settings/keys) を開く
2. 「New SSH key」をクリック
3. 以下の項目を入力する：
   - **Title**: キーを識別する名前（例: `MacBook Pro 2024`）
   - **Key type**: 「Authentication Key」のまま
   - **Key**: `Cmd + V` でコピーした公開鍵を貼り付ける
4. 「Add SSH key」をクリック
5. GitHub のパスワードを入力して確認する

### 7-6. SSH 接続をテストする

```bash
ssh -T git@github.com
```

初回接続時に以下のメッセージが表示されます。

```
The authenticity of host 'github.com (140.82.xxx.xxx)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

`yes` と入力して `Enter` を押します。

成功した場合の出力：

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

`Hi username!` の部分に GitHub のユーザー名が表示されれば、SSH 設定は完了です。

> **うまくいかない場合:** `ssh -vT git@github.com` と `-v` オプションをつけて実行すると、詳細なデバッグ情報が表示されます。

---

## 8. gh CLI のインストール

gh CLI（GitHub CLI）は、コマンドラインから GitHub の操作（Issue 作成、PR 作成など）ができるツールです。
macOS では Homebrew を使って簡単にインストールできます。

### 8-1. Homebrew で gh CLI をインストールする

```bash
brew install gh
```

実行結果の例：

```
==> Downloading https://ghcr.io/v2/homebrew/core/gh/...
==> Installing gh
...
==> Summary
🍺  /opt/homebrew/Cellar/gh/2.x.x: xxx files, xx.xMB
```

### 8-2. バージョンを確認する

```bash
gh --version
```

期待される出力例：

```
gh version 2.x.x (2026-xx-xx)
https://github.com/cli/cli/releases/tag/v2.x.x
```

### 8-3. GitHub にログインする

```bash
gh auth login
```

対話式のメニューが表示されます。

```
? What account do you want to log into?  [Use arrows to move, type to filter]
> GitHub.com
  GitHub Enterprise Server
```

「GitHub.com」を選択して `Enter` を押します。

```
? What is your preferred protocol for Git operations on this host?  [Use arrows to move, type to filter]
> SSH
  HTTPS
```

「SSH」を選択します。

```
? Upload your SSH public key to your GitHub account?  [Use arrows to move, type to filter]
> /Users/username/.ssh/id_ed25519.pub
  Skip
```

先ほど生成した公開鍵（`id_ed25519.pub`）を選択します。

```
? How would you like to authenticate GitHub CLI?  [Use arrows to move, type to filter]
> Login with a web browser
  Paste an authentication token
```

「Login with a web browser」を選択すると、ブラウザが開いて認証できます。

画面に表示されるワンタイムコードをコピーして、ブラウザの認証画面に入力してください。

認証が完了すると：

```
✓ Logged in as username
```

と表示されます。

### 8-4. ログイン状態を確認する

```bash
gh auth status
```

期待される出力例：

```
github.com
  ✓ Logged in to github.com account username (keyring)
  - Active account: true
  - Git operations protocol: ssh
  - Token: gho_****
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'
```

---

## 9. Claude Code のインストール

Claude Code は、ターミナルから Claude AI を操作できるツールです。

### npm 版について

> **重要:** npm を使った `npm install -g @anthropic-ai/claude-code` でのインストールは
> **2025年以降は非推奨**となっています。
> 必ず以下の curl インストーラーを使用してください。

### 必要なアカウント

Claude Code を使用するには、以下のいずれかの Claude アカウントが必要です。

| プラン | Claude Code 利用 |
|---|---|
| 無料プラン | 不可 |
| Pro | 可 |
| Max | 可 |
| Teams | 可 |
| Enterprise | 可 |
| Console（API） | 可 |

> **注意:** 無料プランでは Claude Code を使用できません。
> [claude.ai/upgrade](https://claude.ai/upgrade) からプランを確認・変更してください。

### 9-1. curl インストーラーでインストールする

ターミナルで以下を実行します。

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

インストール中の出力例：

```
Installing Claude Code...
Downloading latest version...
Installing to /Users/username/.local/bin/claude
Setting up shell integration...
Claude Code installed successfully!
```

### 9-2. シェルを再読み込みする

インストール後、PATH の設定を有効にするためにシェルを再読み込みします。

```bash
source ~/.zprofile
```

または、以下のコマンドでも構いません。

```bash
source ~/.zshrc
```

どちらを実行すればよいか分からない場合は、両方実行してください。

ターミナルを一度閉じて再度開いても構いません。

> **`.zprofile` と `.zshrc` の違い:** `.zprofile` はログイン時に1回だけ読み込まれます。`.zshrc` は新しいターミナルタブやウィンドウを開くたびに読み込まれます。PATH の設定は `.zprofile` に書くのが一般的です。

### 9-3. バージョンを確認する

```bash
claude --version
```

期待される出力例：

```
claude 1.x.x
```

### 9-4. Claude Code を起動して認証する

```bash
claude
```

初回起動時にブラウザが開き、Claude アカウントへのログインと認証が求められます。
ログインが完了すると、ターミナルに戻って Claude Code が使えるようになります。

```
✓ Authenticated successfully
Claude Code v1.x.x
Type your message or /help for commands.
>
```

`/exit` または `Ctrl + C` で終了できます。

---

## 10. Brave Search API キーの取得

Claude に Web 検索をさせるための API キーを取得します。
ブラウザで操作するため、OS に関係なく手順は同じです。

### 手順

1. ブラウザで [api-dashboard.search.brave.com/register](https://api-dashboard.search.brave.com/register) を開く
2. メールアドレスとパスワードを入力してアカウントを作成する
3. **クレジットカードを登録する**（詐欺防止のため無料利用でも必須。無料枠内は課金されない）
4. ダッシュボードのプラン選択画面で **Search プラン** を選択する
5. 発行された **Subscription Token** をメモ帳などに控えておく

### 無料枠の内容

| 項目 | 内容 |
|---|---|
| 無料クレジット | 毎月 $5 分が自動付与 |
| クエリ数の目安 | 約 1,000 クエリ/月（$5 ÷ $5/1,000件） |
| レート制限 | 50 クエリ/秒 |
| クレジットカード | 無料利用でも登録必須 |

> **API キーの保管:** 取得した Subscription Token は 03 章の MCP 設定で使います。
> パスワードと同じ扱いで管理し、他人に見せたりコードにそのまま書いたりしないでください。

---

## 11. まとめ — 全項目チェックリスト

すべての手順が完了したか、以下のコマンドで一括確認できます。

```bash
brew --version && \
code --version && \
git --version && \
ssh -T git@github.com && \
gh --version && \
claude --version
```

各項目の期待される結果：

| チェック項目 | 確認コマンド | 期待される出力 |
|---|---|---|
| Homebrew が使える | `brew --version` | `Homebrew 4.x.x` |
| VS Code が使える | `code --version` | バージョン番号（例: `1.97.x`） |
| Git が使える | `git --version` | `git version 2.x.x` |
| GitHub SSH が通る | `ssh -T git@github.com` | `Hi username! You've successfully authenticated` |
| gh CLI が使える | `gh --version` | `gh version 2.x.x` |
| gh CLI でログイン済み | `gh auth status` | `✓ Logged in to github.com` |
| Claude Code が使える | `claude --version` | `claude 1.x.x` |

### すべて完了したら

- [ ] Homebrew をインストールした
- [ ] Apple Silicon Mac の場合、PATH 設定（`~/.zprofile`）を行った
- [ ] VS Code をインストールした
- [ ] `code` コマンドを PATH に追加した
- [ ] `code .` で VS Code が開く
- [ ] Git を Homebrew でインストールした
- [ ] `user.name` と `user.email` を設定した
- [ ] `init.defaultBranch main` を設定した
- [ ] SSH 鍵（ed25519）を生成した
- [ ] `~/.ssh/config` に `UseKeychain yes` を設定した
- [ ] `ssh-add --apple-use-keychain` で Keychain に登録した
- [ ] `pbcopy` で公開鍵をコピーして GitHub に登録した
- [ ] `ssh -T git@github.com` で認証が通る
- [ ] gh CLI を Homebrew でインストールした
- [ ] `gh auth login` で GitHub にログイン済み
- [ ] Claude Code を curl インストーラーでインストールした
- [ ] `claude` コマンドが起動する
- [ ] Brave Search API のアカウントを作成した
- [ ] Subscription Token を取得して手元に控えた

すべてにチェックが入れば、次の章に進む準備ができています。

---

次の章: [01_bash_basics.md](./01_bash_basics.md) — Bash コマンド入門
