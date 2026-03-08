# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Claude と GitHub の使い方を学ぶための初歩的なカリキュラムリポジトリです。

以下の段階的な学習コンテンツを提供します（OS 別準備から始め、番号順に進める）：

| ドキュメント | 内容 |
|---|---|
| 00_preparation_windows.md | WSL・VS Code・Git・GitHub 初期設定（Windows 用） |
| 00_preparation_mac.md | VS Code・Git・GitHub 初期設定（Mac 用） |
| 01_bash_basics.md | ターミナル・Bash 基本コマンド |
| 02_claude_usage.md | Claude Code の使い方 |
| 03_agent_workflow.md | AI エージェントを活用したワークフロー |
| 04_research_with_claude.md | Claude を使った調査・情報収集 |
| 05_github_publish.md | 調査結果を GitHub に公開するワークフロー |
| 06_git_internals.md | Git の内部仕組みと基本概念 |
| 07_markdown.md | Markdown 記法・VS Code プレビュー |
| 08_branch_strategy.md | ブランチ戦略・命名規則 |
| 09_pull_request.md | Pull Request の作成とレビュー |

発展的なトピックは [91_supplement_index.md](./91_supplement_index.md) を参照。

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
