# カリキュラム全体設計

Claude と GitHub の使い方を、実際のクライアント管理シナリオで学ぶ。
**フェーズ1（ソロ）** で基礎を固め、**フェーズ2（チーム）** で実践する構成。

---

## はじめに

| ドキュメント | タイトル | 内容 |
|---|---|---|
| [`00_prologue.md`](./00_prologue.md) | プロローグ | このカリキュラムの目的・全体像 |
| [`01_preparation.md`](./01_preparation.md) | 事前準備 | OS 選択・事前学習リソース |
| [`02_preparation_mac.md`](./02_preparation_mac.md) | 環境準備（Mac） | VS Code・Git・GitHub・Claude セットアップ |
| [`03_preparation_windows.md`](./03_preparation_windows.md) | 環境準備（Windows） | WSL・VS Code・Git・GitHub・Claude セットアップ |

---

## フェーズ1: ソロ作業 — `hakuhodo` リポジトリ

「企業担当者として、博報堂との取引情報を Claude で整理・管理する」シナリオ。

| 章 | ドキュメント | タイトル | 習得スキル | 成果物 |
|---|---|---|---|---|
| 1 | [`04_bash_basics.md`](./04_bash_basics.md) | Bash 基本コマンド | ターミナル操作・ディレクトリ/ファイル管理・パスの概念 | `~/works/hakuhodo/` フォルダ骨格 |
| 2 | [`05_markdown.md`](./05_markdown.md) | Markdown 記法 | 見出し・箇条書き・表・コードブロック・VS Code プレビュー | — |
| 3 | [`06_claude_usage.md`](./06_claude_usage.md) | Claude Code 入門 | Claude への指示・会話形式の操作・基本ワークフロー | — |
| 4 | [`07_agent_workflow.md`](./07_agent_workflow.md) | AI エージェント活用 | MCP 設定・Superpowers・自律エージェントへの依頼 | `notes/info_overview.md`（企業概要） |
| 5 | [`08_git_internals.md`](./08_git_internals.md) | Git の基本概念 | 4領域・コミット・ブランチ・HEAD・`git switch` | hakuhodo リポジトリのコミット履歴 |

### hakuhodo リポジトリの最終構成

```
hakuhodo/
  notes/
    info_overview.md    # 企業概要（事業・規模・特徴）← 03章
  meetings/        # ← フェーズ2の apple で学ぶ（参考）
  tasks/           # ← フェーズ2の apple で学ぶ（参考）
```

---

## フェーズ2: チーム共同作業 — `apple` リポジトリ

「2〜3人チームで、Apple 社の情報を分担してリサーチ・管理する」シナリオ。
フェーズ1で学んだ操作をチームで再実行し、共同作業の難しさと醍醐味を体験する。

| 章 | ドキュメント | タイトル | 習得スキル | 成果物 | フェーズ1との対応 |
|---|---|---|---|---|---|
| 6 | [`09_team_invite.md`](./09_team_invite.md) | GitHub チーム招待 | Collaborator招待・リポジトリクローン・権限管理 | `apple` リポジトリをチームで共有 | 新規 |
| 7 | [`10_research_team.md`](./10_research_team.md) | チームでリサーチ | 役割分担・Claude でリサーチ・まとめ | `notes/overview.md` `notes/products.md` | 4章の復習＋チーム化 |
| 8 | [`11_branch_strategy.md`](./11_branch_strategy.md) | ブランチ戦略 | GitHub Flow・命名規則・Branch Protection Rules | 各自ブランチで `apple/notes/` を分担編集 | 新規 |
| 9 | [`12_pull_request.md`](./12_pull_request.md) | Pull Request | PR作成・レビュー・コメント・マージ | チームで PR をレビューし合う | 新規 |
| 10 | [`13_conflict.md`](./13_conflict.md) | コンフリクト解決 | 意図的コンフリクト発生・解決・学習 | 同一ファイルをチームで編集し解消 | 新規 |

### apple リポジトリの最終構成

```
apple/
  info/
    overview.md    # 企業概要 ← 07章（チームで分担作成）
    contacts.md    # 担当者一覧 ← 07章（チームで分担作成）
  meetings/
    2025-01-meeting.md  # 打ち合わせメモ ← 09章
  tasks/
    todo.md        # タスクリスト ← 10-11章
```

---

## チーム構成と役割

フェーズ2は **2〜3人1組** で進める。

| 役割 | 担当 |
|---|---|
| リポジトリオーナー | `apple` リポジトリを作成・チームを招待する1人 |
| コラボレーター | 招待を受けてクローンし、ブランチで作業する残りのメンバー |

全員が GitHub アカウント作成済みであることが前提。

---

## 学習ロードマップ

```
プロローグ（00）→ 事前準備（01）→ 環境準備（02 or 03）
    │
    ▼
【フェーズ1: ソロ × 博報堂】
Bash基本（04） → Markdown（05） → Claude入門（06） → AIエージェント（07）
    │
    ▼
Git の基本概念（08）── コミット・ブランチを理解
    │
    ▼
【フェーズ2: チーム × Apple】
チーム招待（09）── apple リポジトリ共有
    │
    ▼
チームでリサーチ（10）── 役割分担して apple/notes/ を作成
    │
    ▼
ブランチ戦略（11）
    │
    ▼
Pull Request（12）── チームでレビューし合う
    │
    ▼
コンフリクト解決（13）── チーム開発の難所を体験・克服
```

---

## 補足資料（supplement/）

本編を終えた後、さらに深く学びたい場合の参照先。

| ファイル | 内容 |
|---|---|
| `supplement/git/01_deep_dive.md` | git diff・git log・.git 内部構造・detached HEAD |
| `supplement/git/02_conflict.md` | マージとコンフリクト解決（詳細） |
| `supplement/git/03_team_workflow.md` | チーム開発ワークフロー（詳細） |
| `supplement/git/04_command_reference.md` | コマンドリファレンス早見表 |
| `supplement/claude/01_mcp.md` | MCP と外部ツール連携（Brave Search 含む） |
| `supplement/claude/02_cli.md` | Claude Code CLI 詳細オプション |
| `supplement/claude/03_git_flow.md` | Claude に Git 操作を任せる詳細フロー |
