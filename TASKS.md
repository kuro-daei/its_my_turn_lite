# TASKS.md — カリキュラム改訂作業の進捗

全体設計は [curriculum.md](./curriculum.md) を参照。

---

## 方針

- **フェーズ1（博報堂）**: 01〜05章。ソロ作業。`hakuhodo` リポジトリを題材に。
- **フェーズ2（Apple）**: 06〜12章。チーム作業。`apple` リポジトリを題材に。
- 既存の `client-research` シナリオ・`notes/` フォルダ参照はすべて置き換える。

---

## フェーズ1 — 既存章の修正

### ✅ 完了

| ファイル | 主な変更内容 |
|---|---|
| `03_preparation_windows.md` | Brave Search API キー取得セクションを追加 |
| `02_preparation_mac.md` | Brave Search API キー取得セクションを追加 |
| `05_claude_usage.md` | 起動を `~/works/hakuhodo` から開始に変更 |
| `06_agent_workflow.md` | bash フェーズ → Claude 起動 → researcher/writer/reviewer 会話ログ |
| `07_research_with_claude.md` | Claude 内の作業に統一。03章の続きであることを明記 |

### 🔲 未着手

| ファイル | 修正方針 |
|---|---|
| `04_bash_basics.md` | 題材を `hakuhodo/` フォルダ作成に変更。`client-research/notes/` 参照を削除。作業ディレクトリは `~/works/hakuhodo` |
| `07_research_with_claude.md` | 保存先を `hakuhodo/info/overview.md` `hakuhodo/info/contacts.md` に変更 |
| `08_git_internals.md` | 旧06章を改訂。hakuhodo リポジトリのコミット履歴を例に使う。章番号を05に変更 |

---

## フェーズ2 — 新規作成・既存章の移植

### 🔲 新規作成

| ファイル | 内容 |
|---|---|
| `09_team_invite.md` | Collaborator 招待・クローン・権限管理。apple リポジトリをチームで共有する |
| `10_research_team.md` | 旧04章を Apple × チーム版に改訂。役割分担してリサーチ・まとめ |
| `15_conflict.md` | 意図的コンフリクト発生・解決。同一ファイルをチームで編集し解消 |

### 🔲 既存章の移植（旧番号 → 新番号）

| 旧ファイル | 新ファイル | 修正方針 |
|---|---|---|
| `05_github_publish.md` | `11_github_publish.md` | Apple シナリオに変更。チームで push・PR を体験 |
| `07_markdown.md` | `12_markdown.md` | 題材を `apple/meetings/2025-01-meeting.md` に変更 |
| `08_branch_strategy.md` | `13_branch_strategy.md` | Apple シナリオ・チーム前提に。各自がブランチで `apple/info/` を分担編集 |
| `09_pull_request.md` | `14_pull_request.md` | Apple シナリオ・チームでレビューし合う形に |

---

## ファイルリネーム作業

既存章のファイル名を変更する（内容修正の前に実施）。

| 現在のファイル名 | 変更後 |
|---|---|
| `05_github_publish.md` | `11_github_publish.md` |
| `06_git_internals.md` | `08_git_internals.md` |
| `07_markdown.md` | `12_markdown.md` |
| `08_branch_strategy.md` | `13_branch_strategy.md` |
| `09_pull_request.md` | `14_pull_request.md` |

---

## 共通の執筆ルール（CLAUDE.md より）

- 操作手順はすべて Claude への指示形式（`>` プロンプト）で記述する
- コマンドラインが必須の操作のみ `> **⚠️ コマンドライン操作:**` 注意書きを付ける
- `git checkout` は使わず `git switch` で統一する
- フェーズ1のシナリオ: 作業ディレクトリ `~/works/hakuhodo`、リポジトリ名 `hakuhodo`
- フェーズ2のシナリオ: 作業ディレクトリ `~/works/apple`、リポジトリ名 `apple`
- メモファイルはハイフン区切り（例: `info/overview.md`、`meetings/2025-01-meeting.md`）
