# TASKS.md — カリキュラム改訂作業の進捗

全体設計は [curriculum.md](./curriculum.md) を参照。

---

## 方針

- **フェーズ1（博報堂）**: 01〜04章。ソロ作業。`hakuhodo` リポジトリを題材に。
- **フェーズ2（Apple）**: 05〜12章。チーム作業。`apple` リポジトリを題材に。
- 既存の `client-research` シナリオ・`notes/` フォルダ参照はすべて置き換える。

---

## フェーズ1 — 既存章の修正

### ✅ 完了

| ファイル | 主な変更内容 |
|---|---|
| `03_preparation_windows.md` | Brave Search API キー取得セクションを追加 |
| `02_preparation_mac.md` | Brave Search API キー取得セクションを追加 |
| `06_claude_usage.md` | 起動を `~/works/hakuhodo` から開始に変更 |
| `07_agent_workflow.md` | bash フェーズ → Claude 起動 → researcher/reviewer 会話ログ。`07_research_with_claude.md` を統合・削除 |

### 🔲 未着手

| ファイル | 修正方針 |
|---|---|
| `04_bash_basics.md` | 題材を `hakuhodo/` フォルダ作成に変更。`client-research/notes/` 参照を削除。作業ディレクトリは `~/works/hakuhodo` |
| `08_git_internals.md` | 旧06章を改訂。hakuhodo リポジトリのコミット履歴を例に使う |

---

## フェーズ2 — 新規作成・既存章の移植

### 🔲 新規作成

| ファイル | 内容 |
|---|---|
| `09_team_invite.md` | Collaborator 招待・クローン・権限管理。apple リポジトリをチームで共有する |
| `10_research_team.md` | 旧04章を Apple × チーム版に改訂。役割分担してリサーチ・まとめ |
| `13_conflict.md` | 意図的コンフリクト発生・解決。同一ファイルをチームで編集し解消 |

### 🔲 既存章の移植（旧番号 → 新番号）

| 旧ファイル | 新ファイル | 修正方針 |
|---|---|---|
| `07_markdown.md` | `05_markdown.md` | 題材を `apple/meetings/2025-01-meeting.md` に変更 |
| `08_branch_strategy.md` | `11_branch_strategy.md` | Apple シナリオ・チーム前提に。各自がブランチで `apple/info/` を分担編集 |
| `09_pull_request.md` | `12_pull_request.md` | Apple シナリオ・チームでレビューし合う形に |

---

## 共通の執筆ルール（CLAUDE.md より）

- 操作手順はすべて Claude への指示形式（`>` プロンプト）で記述する
- コマンドラインが必須の操作のみ `> **⚠️ コマンドライン操作:**` 注意書きを付ける
- `git checkout` は使わず `git switch` で統一する
- フェーズ1のシナリオ: 作業ディレクトリ `~/works/hakuhodo`、リポジトリ名 `hakuhodo`
- フェーズ2のシナリオ: 作業ディレクトリ `~/works/apple`、リポジトリ名 `apple`
- メモファイルはハイフン区切り（例: `info/overview.md`、`meetings/2025-01-meeting.md`）
