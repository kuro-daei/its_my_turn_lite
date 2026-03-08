# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude と GitHub の使い方を学ぶための初歩的なカリキュラムリポジトリです。

参考ドキュメント（`../road_to_deploy/doc/`）に基づき、以下の段階的な学習コンテンツを提供します：

| ドキュメント | 内容 |
|---|---|
| 01_setup.md | WSL・VS Code・Git・GitHub 初期設定 |
| 02_github_start.md | リポジトリ作成・commit・push 基礎 |
| 03_git_branch.md | ブランチの概念・操作・マージ |
| 04_markdown.md | Markdown 記法・VS Code プレビュー |
| 05_command_reference.md | Git コマンド早見表 |

対象読者：Git・GitHub・WSL 初心者（Windows 10/11 + インターネット接続 + GitHub アカウントが前提）

## Git ワークフロー

このリポジトリで使う基本フローです。

```
git pull
git checkout -b feature/作業内容
# ... 編集 ...
git add <ファイル>
git commit -m "feat: 変更内容"
git push -u origin feature/作業内容
# → main にマージ後、ブランチ削除
```

## コミットメッセージ規則

Conventional Commits 形式を使用します：

| prefix | 用途 |
|---|---|
| `feat:` | 新機能・新コンテンツ追加 |
| `fix:` | 誤字・誤記修正 |
| `docs:` | ドキュメント更新 |
| `refactor:` | 構成変更（内容変更なし） |

## ブランチ命名規則

| パターン | 用途 |
|---|---|
| `feature/機能名` | 新機能・新コンテンツ |
| `fix/バグ内容` | 修正 |
| `docs/更新内容` | ドキュメント |

## SSH 設定（初回のみ）

```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub  # GitHub に登録
ssh -T git@github.com      # 接続テスト
```

## リモートリポジトリ

`git@github.com:kuro-daei/its_my_turn_lite.git`
