# カリキュラム全体設計

Claude と GitHub の使い方を、実際のクライアント管理シナリオで学ぶ。
**フェーズ1（ソロ）** で基礎を固め、**フェーズ2（チーム）** で実践する構成。

---

## フェーズ1: ソロ作業 — `hakuhodo` リポジトリ

「企業担当者として、博報堂との取引情報を Claude で整理・管理する」シナリオ。

| 章 | ドキュメント | タイトル | 習得スキル | 成果物 |
|---|---|---|---|---|
| — | `00_preparation_windows.md` | 環境準備（Windows） | WSL・VS Code・Git・GitHub・Claude セットアップ | — |
| — | `00_preparation_mac.md` | 環境準備（Mac） | VS Code・Git・GitHub・Claude セットアップ | — |
| 1 | `01_bash_basics.md` | Bash 基本コマンド | ターミナル操作・ディレクトリ/ファイル管理・パスの概念 | `~/works/hakuhodo/` フォルダ骨格 |
| 2 | `02_claude_usage.md` | Claude Code 入門 | Claude への指示・会話形式の操作・基本ワークフロー | — |
| 3 | `03_agent_workflow.md` | AI エージェント活用 | MCP 設定・Superpowers・自律エージェントへの依頼 | — |
| 4 | `04_research_with_claude.md` | Claude でリサーチ | 情報収集・整理・Markdown メモ作成 | `info/overview.md`（企業概要）`info/contacts.md`（担当者一覧） |
| 5 | `05_git_internals.md` | Git の基本概念 | 4領域・コミット・ブランチ・HEAD・`git switch` | hakuhodo リポジトリのコミット履歴 |

### hakuhodo リポジトリの最終構成

```
hakuhodo/
  info/
    overview.md    # 企業概要（事業・規模・特徴）← 04章
    contacts.md    # 担当者一覧（部署・連絡先）← 04章
  meetings/        # ← フェーズ2の apple で学ぶ（参考）
  tasks/           # ← フェーズ2の apple で学ぶ（参考）
```

---

## フェーズ2: チーム共同作業 — `apple` リポジトリ

「2〜3人チームで、Apple 社の情報を分担してリサーチ・管理する」シナリオ。
フェーズ1で学んだ操作をチームで再実行し、共同作業の難しさと醍醐味を体験する。

| 章 | ドキュメント | タイトル | 習得スキル | 成果物 | フェーズ1との対応 |
|---|---|---|---|---|---|
| 6 | `06_team_invite.md` | GitHub チーム招待 | Collaborator招待・リポジトリクローン・権限管理 | `apple` リポジトリをチームで共有 | 新規 |
| 7 | `07_research_team.md` | チームでリサーチ | 役割分担・Claude でリサーチ・まとめ | `info/overview.md` `info/contacts.md` | 04章の復習＋チーム化 |
| 8 | `08_github_publish.md` | GitHub に公開（チーム） | push・PR の基本をチームで体験 | `info/` を GitHub に公開 | 旧05-06章の復習 |
| 9 | `09_markdown.md` | Markdown | 記法・VS Code プレビュー・ミーティングメモ作成 | `meetings/2025-01-meeting.md` | 旧07章の復習 |
| 10 | `10_branch_strategy.md` | ブランチ戦略 | GitHub Flow・命名規則・Branch Protection Rules | 各自ブランチで `info/` を分担編集 | 旧08章の復習＋チーム化 |
| 11 | `11_pull_request.md` | Pull Request | PR作成・レビュー・コメント・マージ | チームで PR をレビューし合う | 旧09章の復習＋チーム化 |
| 12 | `12_conflict.md` | コンフリクト解決 | 意図的コンフリクト発生・解決・学習 | 同一ファイルをチームで編集し解消 | 新規 |

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
環境準備（00）
    │
    ▼
【フェーズ1: ソロ × 博報堂】
Bash基本（01） → Claude入門（02） → AIエージェント（03）
    │
    ▼
Claude でリサーチ（04）── hakuhodo/info/ を作成
    │
    ▼
Git の基本概念（05）── コミット・ブランチを理解
    │
    ▼
【フェーズ2: チーム × Apple】
チーム招待（06）── apple リポジトリ共有
    │
    ▼
チームでリサーチ（07）── 役割分担して apple/info/ を作成
    │
    ▼
GitHub に公開（08）→ Markdown（09）→ ブランチ戦略（10）
    │
    ▼
Pull Request（11）── チームでレビューし合う
    │
    ▼
コンフリクト解決（12）── チーム開発の難所を体験・克服
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
