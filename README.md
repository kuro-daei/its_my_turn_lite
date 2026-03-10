# Claude × GitHub 入門カリキュラム

Claude Code と GitHub の使い方を、**実際の業務シナリオで手を動かしながら**学ぶハンズオン型カリキュラムです。

---

## 対象読者

- Git・GitHub をほぼ使ったことがない方
- Claude Code を業務で活用したい方
- Windows（WSL）または Mac 環境の方

---

## カリキュラム構成

2つのフェーズで段階的に学びます。

### はじめに

| ドキュメント | 内容 |
|---|---|
| [`00_prologue.md`](./00_prologue.md) | プロローグ（読み物） |
| [`01_preparation.md`](./01_preparation.md) | 事前準備（OS 選択・事前学習リソース） |
| [`02_preparation_mac.md`](./02_preparation_mac.md) | 環境準備（Mac: VS Code・Git・GitHub・Claude） |
| [`03_preparation_windows.md`](./03_preparation_windows.md) | 環境準備（Windows: WSL・VS Code・Git・GitHub・Claude） |

### フェーズ1: ソロ作業（博報堂シナリオ）

企業担当者として、クライアント情報を Claude で整理・管理するシナリオです。

| 章 | ドキュメント | 内容 |
|---|---|---|
| 1 | [`04_bash_basics.md`](./04_bash_basics.md) | ターミナル・Bash 基本コマンド |
| 2 | [`05_claude_usage.md`](./05_claude_usage.md) | Claude Code の使い方 |
| 3 | [`06_agent_workflow.md`](./06_agent_workflow.md) | AI エージェントを活用したワークフロー |
| 4 | [`07_research_with_claude.md`](./07_research_with_claude.md) | Claude を使った調査・情報収集 |
| 5 | [`08_git_internals.md`](./08_git_internals.md) | Git の基本概念（4領域・コミット・ブランチ・HEAD） |

### フェーズ2: チーム共同作業（Apple シナリオ）

2〜3人チームで、クライアント情報を分担してリサーチ・管理するシナリオです。

| 章 | ドキュメント | 内容 |
|---|---|---|
| 6 | [`09_team_invite.md`](./09_team_invite.md) | GitHub チーム招待・Collaborator 権限管理 |
| 7 | [`10_research_team.md`](./10_research_team.md) | チームでのリサーチ・役割分担 |
| 8 | [`11_github_publish.md`](./11_github_publish.md) | GitHub への公開（push・PR 入門） |
| 9 | [`12_markdown.md`](./12_markdown.md) | Markdown 記法・VS Code プレビュー |
| 10 | [`13_branch_strategy.md`](./13_branch_strategy.md) | ブランチ戦略・GitHub Flow・Branch Protection |
| 11 | [`14_pull_request.md`](./14_pull_request.md) | Pull Request の作成・レビュー・マージ |
| 12 | [`15_conflict.md`](./15_conflict.md) | コンフリクト発生と解決 |

---

## 学習ロードマップ

```
プロローグ（00）→ 事前準備（01）→ 環境準備（02 or 03）
    │
    ▼
【フェーズ1: ソロ × 博報堂】
Bash基本（04） → Claude入門（05） → AIエージェント（06）
    │
    ▼
Claude でリサーチ（07）── hakuhodo/info/ を作成
    │
    ▼
Git の基本概念（08）── コミット・ブランチを理解
    │
    ▼
【フェーズ2: チーム × Apple】
チーム招待（09）── apple リポジトリ共有
    │
    ▼
チームでリサーチ（10）── 役割分担して apple/info/ を作成
    │
    ▼
GitHub に公開（11）→ Markdown（12）→ ブランチ戦略（13）
    │
    ▼
Pull Request（14）── チームでレビューし合う
    │
    ▼
コンフリクト解決（15）── チーム開発の難所を体験・克服
```

---

## 補足資料

本編完了後、さらに深く学びたい場合は [`supplement_index.md`](./supplement_index.md) を参照してください。

| カテゴリ | 内容 |
|---|---|
| Git 深掘り | `git diff`・`git log`・.git 内部・detached HEAD・コンフリクト詳細 |
| Claude 活用 | 効果的なプロンプト・MCP 詳細設定・Claude に Git 操作を任せる方法 |

---

## 前提条件

- GitHub アカウント（無料）
- Windows 10/11 または Mac
- インターネット接続
- Claude Code（各章の準備手順でセットアップします）
