# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude と GitHub の使い方を学ぶための初歩的なカリキュラムリポジトリです。

ハンズオン形式で各章を連続実施します。座学ではないので注意が必要です。

Claude Code 内の処理と Teminal での処理が混同されるので、基本的には Claude Code の Tab と Terminal 操作の Tab を分けて操作させます。

以下の段階的な学習コンテンツを提供します（OS 別準備から始め、番号順に進める）：

| ドキュメント | 内容 |
|---|---|
| 00_prologue.md | プロローグ（読み物） |
| 01_preparation.md | 事前準備（OS 選択・事前学習リソース） |
| 02_preparation_mac.md | VS Code・Git・GitHub 初期設定（Mac 用） |
| 03_preparation_windows.md | WSL・VS Code・Git・GitHub 初期設定（Windows 用） |
| 04_bash_basics.md | ターミナル・Bash 基本コマンド |
| 05_markdown.md | Markdown 記法・VS Code プレビュー |
| 06_claude_usage.md | Claude Code の使い方 |
| 07_agent_workflow.md | AI エージェントを活用したワークフロー |
| 08_git_internals.md | Git の基本概念（4領域・コミット・HEAD） |
| 09_team_invite.md | GitHub チーム招待・Collaborator 権限管理 |
| 10_research_team.md | チームでのリサーチ・役割分担 |
| 11_branch_strategy.md | ブランチ戦略・命名規則 |
| 12_pull_request.md | Pull Request の作成とレビュー |
| 13_conflict.md | コンフリクト発生と解決 |

発展的なトピックは [supplement_index.md](./supplement_index.md) を参照。

対象読者：Git・GitHub・WSL 初心者（Windows 10/11 または Mac + インターネット接続 + GitHub アカウントが前提）

## Git ワークフロー

このリポジトリで使う基本フローです。

```
git pull
git switch -c feature/作業内容
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
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub  # GitHub に登録
ssh -T git@github.com      # 接続テスト
```

## リモートリポジトリ

`git@github.com:kuro-daei/its_my_turn_lite.git`

## コンテンツ執筆スタイル

- 操作手順はすべて Claude への指示形式（`>` プロンプト）で記述する
- コマンドラインが必須の操作には `> **⚠️ コマンドライン操作:**` 注意書きを付ける
- 調査メモのファイルは `info/` フォルダに格納、名前はハイフン区切り（例: `info/financial-research.md`）
- シナリオの作業ディレクトリは `~/works/client-research`、リポジトリ名は `client-research`
- `git checkout` は使わず `git switch` で統一する

## 編集作業

調査・編集・レビューは `doc-writer-team` が行うようにしてください。

作業の進捗・方針は [TASKS.md](./TASKS.md) を参照してください。
