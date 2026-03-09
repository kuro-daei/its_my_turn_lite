# 00. 事前準備（Windows 版）— 開発環境のセットアップ

> **macOS をお使いの方へ:** [00_preparation_mac.md](./00_preparation_mac.md) を参照してください。

この章では、以降の学習で使うすべてのツールをインストールします。
手順通りに進めれば、Git・GitHub・Claude Code が使える状態になります。

---

## 1. この章でやること

以下の順序でツールをセットアップします。
依存関係があるため、**必ずこの順番で実施してください**。

```
+------------------+
|  Windows 10/11   |
+--------+---------+
         |
         v
+------------------+     PowerShell (管理者) で wsl --install
|   WSL (Ubuntu)   |
+--------+---------+
         |
         v
+------------------+     code.visualstudio.com からインストール
|    VS Code       |
+--------+---------+
         |
         v
+------------------+     sudo apt install git
|      Git         |
+--------+---------+
         |
         v
+------------------+     ssh-keygen → GitHub に公開鍵を登録
|  GitHub (SSH)    |
+--------+---------+
         |
         v
+------------------+     公式 APT リポジトリ経由でインストール
|    gh CLI        |
+--------+---------+
         |
         v
+------------------+     curl インストーラーでインストール
|  Claude Code     |
+--------+---------+
         |
         v
+------------------+
|  完了！          |
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
| OS | Windows 10 Build 19041 以上、または Windows 11 | スタートメニュー → 「winver」と入力して実行 |
| インターネット接続 | 有線・無線どちらでも可 | ブラウザで任意のサイトを開いて確認 |
| GitHub アカウント | 無料アカウントで可 | [github.com](https://github.com) でログイン確認 |
| Claude アカウント | Pro / Max / Teams / Enterprise / Console のいずれか（**無料プランは不可**） | [claude.ai](https://claude.ai) でプランを確認 |
| ディスク空き容量 | 10 GB 以上を推奨 | エクスプローラー → PC → ローカルディスク |

### Windows のビルド番号を確認する

```powershell
winver
```

「バージョン 2004（OS ビルド 19041）」以上であれば問題ありません。
もし 19041 未満の場合は、Windows Update で最新版に更新してから作業を始めてください。

> **注意:** Windows 10 の場合、Home / Pro / Education のいずれのエディションでも WSL 2 を使用できます。

---

## 3. WSL のインストール

WSL（Windows Subsystem for Linux）は、Windows 上で Linux を動かす仕組みです。
Git の操作や Claude Code の実行は、WSL 内の Ubuntu で行います。

### 3-1. PowerShell を管理者として起動する

1. スタートメニューを開く
2. 「PowerShell」と入力する
3. 「Windows PowerShell」を**右クリック**して「管理者として実行」を選ぶ
4. 「このアプリがデバイスに変更を加えることを許可しますか？」→「はい」をクリック

タイトルバーに「管理者: Windows PowerShell」と表示されていれば成功です。

### 3-2. WSL をインストールする

管理者権限の PowerShell で以下のコマンドを実行します。

```powershell
wsl --install
```

実行結果の例：

```
インストール中: 仮想マシン プラットフォーム
仮想マシン プラットフォーム はインストールされました。
インストール中: Linux 用 Windows サブシステム
Linux 用 Windows サブシステム はインストールされました。
インストール中: Ubuntu
Ubuntu はインストールされました。
このシステムに変更を適用するために、操作を再起動してください。
```

> **注意:** `wsl --install` を実行すると、Ubuntu が既定のディストリビューションとして自動インストールされます。
> 別のディストリビューションを使いたい場合は `wsl --install -d DistroName` と指定できますが、
> この教材では Ubuntu を前提としています。

### 3-3. Windows を再起動する

インストール完了後、**必ず再起動**してください。
再起動しないと次のステップが正しく動作しません。

```powershell
Restart-Computer
```

または、スタートメニュー → 電源アイコン → 「再起動」でも構いません。

### 3-4. Ubuntu の初期設定をする

再起動後、自動的に Ubuntu のターミナルが開きます。
開かない場合は、スタートメニューから「Ubuntu」を起動してください。

初回起動時に、Unix ユーザー名とパスワードの設定を求められます。

```
Enter new UNIX username: your-username
New password:
Retype new password:
passwd: password updated successfully
```

> **注意:**
> - ユーザー名は小文字英字とハイフン・アンダースコアのみ使用可能です。
> - パスワード入力時は画面に何も表示されません（セキュリティ上の仕様です）。
> - このユーザー名とパスワードは Ubuntu 内でのみ使うものです。Windows のアカウントとは別物です。

### 3-5. パッケージリストを更新する

Ubuntu が起動したら、まずパッケージリストを最新にします。

```bash
sudo apt update && sudo apt upgrade -y
```

実行結果の例（最後の数行）：

```
Reading package lists... Done
Building dependency tree... Done
...
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

> **`sudo` とは？** スーパーユーザー（管理者）権限でコマンドを実行するためのプレフィックスです。
> 初回実行時にパスワードの入力を求められます。設定したパスワードを入力してください。

### 3-6. インストールの確認

```bash
uname -a
```

期待される出力例：

```
Linux DESKTOP-XXXXXXX 5.15.167.4-microsoft-standard-WSL2 #1 SMP ... x86_64 x86_64 x86_64 GNU/Linux
```

`WSL2` という文字列が含まれていれば、WSL 2 が正常に動作しています。

---

## 4. VS Code のインストール

VS Code（Visual Studio Code）はテキストエディタです。
コードを書いたり、Markdown ファイルをプレビューしたりするために使います。

### 4-1. VS Code をダウンロードする

ブラウザで [https://code.visualstudio.com](https://code.visualstudio.com) にアクセスし、
「Download for Windows」をクリックしてインストーラーをダウンロードします。

### 4-2. インストーラーを実行する

ダウンロードしたインストーラー（`VSCodeUserSetup-x64-*.exe`）をダブルクリックして実行します。

インストール中のオプション設定で、以下の項目にチェックを入れることを推奨します：

- [x] エクスプローラーのファイル コンテキスト メニューに [Code で開く] アクションを追加する
- [x] エクスプローラーのディレクトリ コンテキスト メニューに [Code で開く] アクションを追加する
- [x] PATH への追加（再起動後に使用可能）

> **注意:** VS Code は **Windows 側**にインストールします。WSL の中ではありません。

### 4-3. WSL 拡張機能をインストールする

VS Code から WSL 内のファイルを編集するために「WSL 拡張機能」が必要です。

1. VS Code を起動する
2. 左側のアクティビティバーから拡張機能アイコン（四角が4つ）をクリック
3. 検索ボックスに「WSL」と入力
4. 「WSL」（発行元: Microsoft）を選んで「インストール」をクリック

または、Ubuntu のターミナルから以下のコマンドでも拡張機能をインストールできます。

```bash
code --install-extension ms-vscode-remote.remote-wsl
```

### 4-4. VS Code が WSL から起動できるか確認する

Ubuntu のターミナルで以下を実行します。

```bash
code .
```

VS Code が起動し、左下に「WSL: Ubuntu」と表示されれば成功です。

> **初回起動時の注意:** 初めて `code .` を実行すると、VS Code サーバーのダウンロードが始まります。
> 数分かかる場合があります。完了するまで待ってください。

---

## 5. Git のインストールと初期設定

Git はバージョン管理ツールです。ファイルの変更履歴を記録し、チームで共同作業するために使います。

### 5-1. Git をインストールする

Ubuntu のターミナルで以下を実行します。

```bash
sudo apt install git -y
```

実行結果の例：

```
Reading package lists... Done
Building dependency tree... Done
...
Setting up git (1:2.43.0-1ubuntu7.1) ...
```

### 5-2. バージョンを確認する

```bash
git --version
```

期待される出力例：

```
git version 2.43.0
```

バージョン番号が表示されれば、インストール成功です。

### 5-3. ユーザー情報を設定する

Git のコミット（変更の記録）に名前とメールアドレスが紐付けられます。
GitHub に登録したものと同じ情報を設定することを推奨します。

```bash
git config --global user.name "あなたの名前"
git config --global user.email "your-email@example.com"
```

> **注意:** `user.email` には GitHub に登録したメールアドレスを使ってください。
> 異なるメールアドレスを使うと、GitHub のコントリビューションが正しく記録されない場合があります。

### 5-4. デフォルトブランチ名を設定する

GitHub の既定ブランチ名は `main` です。Git 側も合わせておきます。

```bash
git config --global init.defaultBranch main
```

### 5-5. エディタを VS Code に設定する

コミットメッセージを編集するエディタを VS Code に設定します。

```bash
git config --global core.editor "code --wait"
```

### 5-6. 設定内容を確認する

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

## 6. GitHub SSH 設定

GitHub へのアクセスには HTTPS と SSH の2つの方法があります。
ここでは**SSH**を使います。SSH は一度設定すれば、パスワード入力なしに GitHub を操作できます。

### 6-1. SSH 鍵を生成する

Ubuntu のターミナルで以下を実行します。
`your-email@example.com` の部分は GitHub に登録したメールアドレスに変更してください。

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

実行中に以下の質問が表示されます。

```
Enter file in which to save the key (/home/username/.ssh/id_ed25519):
```

そのまま Enter を押します（デフォルトの場所に保存されます）。

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

パスフレーズは設定することを推奨しますが、空でも構いません。Enter を2回押すとパスフレーズなしで生成されます。

実行結果の例：

```
Your identification has been saved in /home/username/.ssh/id_ed25519
Your public key has been saved in /home/username/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your-email@example.com
```

### 6-2. ssh-agent を起動して鍵を登録する

```bash
eval "$(ssh-agent -s)"
```

期待される出力例：

```
Agent pid 1234
```

続けて鍵を登録します。

```bash
ssh-add ~/.ssh/id_ed25519
```

期待される出力例：

```
Identity added: /home/username/.ssh/id_ed25519 (your-email@example.com)
```

### 6-3. 公開鍵を表示してコピーする

```bash
cat ~/.ssh/id_ed25519.pub
```

出力例：

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx your-email@example.com
```

この1行全体をコピーします（`ssh-ed25519` から始まりメールアドレスで終わる行です）。

### 6-4. GitHub に公開鍵を登録する

1. ブラウザで [https://github.com/settings/keys](https://github.com/settings/keys) を開く
2. 「New SSH key」をクリック
3. 以下の項目を入力する：
   - **Title**: キーを識別する名前（例: `WSL Ubuntu on MyPC`）
   - **Key type**: 「Authentication Key」のまま
   - **Key**: コピーした公開鍵を貼り付ける
4. 「Add SSH key」をクリック
5. GitHub のパスワードを入力して確認する

### 6-5. SSH 接続をテストする

```bash
ssh -T git@github.com
```

初回接続時に以下のメッセージが表示されます。

```
The authenticity of host 'github.com (140.82.xxx.xxx)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

`yes` と入力して Enter を押します。

成功した場合の出力：

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

`Hi username!` の部分に GitHub のユーザー名が表示されれば、SSH 設定は完了です。

> **うまくいかない場合:** `ssh -vT git@github.com` と `-v` オプションをつけて実行すると、
> 詳細なデバッグ情報が表示されます。

---

## 7. gh CLI のインストール

gh CLI（GitHub CLI）は、コマンドラインから GitHub の操作（Issue 作成、PR 作成など）ができるツールです。

### なぜ Snap 版ではなく APT リポジトリを使うのか

Ubuntu には `snap` というパッケージ管理ツールもありますが、**gh CLI の Snap 版は SSH キーにアクセスできない制限**があります。
GitHub の認証で SSH を使う場合、Snap 版は正しく動作しません。
必ず以下の**公式 APT リポジトリ**を使う方法でインストールしてください。

> **重要:** `sudo snap install gh` は使用しないでください。SSH 認証が機能しない場合があります。

### 7-1. 必要なパッケージをインストールする

```bash
sudo apt install curl gpg -y
```

### 7-2. GitHub CLI の公式 GPG キーを登録する

```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
```

```bash
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
```

### 7-3. APT リポジトリを追加する

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
  | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
```

### 7-4. gh CLI をインストールする

```bash
sudo apt update && sudo apt install gh -y
```

### 7-5. バージョンを確認する

```bash
gh --version
```

期待される出力例：

```
gh version 2.x.x (2026-xx-xx)
https://github.com/cli/cli/releases/tag/v2.x.x
```

### 7-6. GitHub にログインする

```bash
gh auth login
```

対話式のメニューが表示されます。

```
? What account do you want to log into?  [Use arrows to move, type to filter]
> GitHub.com
  GitHub Enterprise Server
```

「GitHub.com」を選択して Enter を押します。

```
? What is your preferred protocol for Git operations on this host?  [Use arrows to move, type to filter]
> SSH
  HTTPS
```

「SSH」を選択します。

```
? Upload your SSH public key to your GitHub account?  [Use arrows to move, type to filter]
> /home/username/.ssh/id_ed25519.pub
  Skip
```

先ほど生成した公開鍵を選択します。

```
? How would you like to authenticate GitHub CLI?  [Use arrows to move, type to filter]
> Login with a web browser
  Paste an authentication token
```

「Login with a web browser」を選択すると、ブラウザが開いて認証できます。

認証が完了すると：

```
✓ Logged in as username
```

と表示されます。

### 7-7. ログイン状態を確認する

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

## 8. Claude Code のインストール

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

### 8-1. curl インストーラーでインストールする

Ubuntu のターミナルで以下を実行します。

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

インストール中の出力例：

```
Installing Claude Code...
Downloading latest version...
Installing to /home/username/.local/bin/claude
Setting up shell integration...
Claude Code installed successfully!
```

### 8-2. シェルを再読み込みする

インストール後、PATH の設定を有効にするためにシェルを再読み込みします。

```bash
source ~/.bashrc
```

または、ターミナルを一度閉じて再度開いても構いません。

### 8-3. バージョンを確認する

```bash
claude --version
```

期待される出力例：

```
claude 1.x.x
```

### 8-4. Claude Code を起動して認証する

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

`/exit` または Ctrl+C で終了できます。

---

## 9. まとめ — 全項目チェックリスト

すべての手順が完了したか、以下のコマンドで一括確認できます。

```bash
uname -a && \
code --version && \
git --version && \
ssh -T git@github.com && \
gh --version && \
claude --version
```

各項目の期待される結果：

| チェック項目 | 確認コマンド | 期待される出力 |
|---|---|---|
| WSL 2 が動作している | `uname -a` | `WSL2` を含む文字列 |
| VS Code が使える | `code --version` | バージョン番号（例: `1.97.x`） |
| Git が使える | `git --version` | `git version 2.x.x` |
| GitHub SSH が通る | `ssh -T git@github.com` | `Hi username! You've successfully authenticated` |
| gh CLI が使える | `gh --version` | `gh version 2.x.x` |
| gh CLI でログイン済み | `gh auth status` | `✓ Logged in to github.com` |
| Claude Code が使える | `claude --version` | `claude 1.x.x` |

### すべて完了したら

- [ ] WSL（Ubuntu）がインストールされている
- [ ] Ubuntu のユーザー名とパスワードを設定した
- [ ] `sudo apt update` が正常に完了している
- [ ] VS Code がインストールされ、WSL 拡張機能が有効になっている
- [ ] `code .` で VS Code が開く
- [ ] Git がインストールされ、`user.name` と `user.email` を設定した
- [ ] SSH 鍵（ed25519）を生成した
- [ ] GitHub に公開鍵を登録した
- [ ] `ssh -T git@github.com` で認証が通る
- [ ] gh CLI を APT リポジトリからインストールした
- [ ] `gh auth login` で GitHub にログイン済み
- [ ] Claude Code を curl インストーラーでインストールした
- [ ] `claude` コマンドが起動する

すべてにチェックが入れば、次の章に進む準備ができています。

---

次の章: [01_bash_basics.md](./01_bash_basics.md) — Bash コマンド入門
