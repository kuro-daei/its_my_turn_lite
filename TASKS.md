# TASKS.md — ストーリー化作業の進捗

全体のストーリー：「client-research プロジェクトを Claude で進める」という一連の流れで
01〜09 を繋げる。03 以降は Claude の中の作業をイメージ、コマンドラインは最小限。

---

## 完了済み

| ファイル | 主な変更内容 |
|---|---|
| `00_preparation_windows.md` | Brave Search API キー取得セクションを追加（セクション 9） |
| `00_preparation_mac.md` | Brave Search API キー取得セクションを追加（セクション 10） |
| `01_bash_basics.md` | `client-research` フォルダ作成を題材にしたストーリーに全面改訂。コマンドを上から順に実行すると動く流れに |
| `02_claude_usage.md` | 起動を `~/works/client-research` から開始。フォルダ信頼確認の画面を追加。実例を Git 系から削除しリサーチ系に変更。ワンショットモードを supplement へ移動 |
| `03_agent_workflow.md` | bash フェーズ（MCP 設定・Superpowers インストール）→ Claude 起動 → researcher/writer/reviewer の会話ログ、に全面改訂 |
| `04_research_with_claude.md` | bash コマンドを削除し Claude 内の作業に統一。冒頭に 03 の続きである旨を追記 |
| `05_github_publish.md` | 旧 05・06 を統合。Git の基本概念 + Claude 会話形式で GitHub 公開の流れを構成 |
| `06_git_internals.md` | 旧 06 をコンパクトな概念ページに再構成。詳細は supplement/git/01_deep_dive.md へ |
| `supplement/` | claude/ と git/ のサブディレクトリに再編。全 7 ファイルのリンクを更新 |

---

## 未着手（これから修正する章）

| ファイル | 修正方針 |
|---|---|
| `05_github_publish.md` | 再修正予定 |
| `06_git_internals.md` | 再修正予定 |
| `07_markdown.md` | Claude 前提に。コマンドライン操作を最小化。実例は client-research のメモ作成に合わせる |
| `08_branch_strategy.md` | Claude 前提に。ブランチ操作は Claude に指示する形式で |
| `09_pull_request.md` | Claude 前提に。PR 作成・レビュー対応を Claude 会話形式で |

---

## 方針メモ

- **ストーリー軸**: 04 の「Claude でリサーチ」を全体の目的とし、各章がその準備・実行・公開の流れになるよう構成
- **フォルダ名**: `client-research`（`stock-research` から変更済み）
- **03 以降**: bash フェーズがある場合は冒頭に集約し、その後 `claude` 起動 → Claude 内作業の順
- **ファイル作成の実例は避ける**: Claude への質問・調査依頼を中心にした実例にする
- **Git 系の実例は本編から除外**: supplement/claude/03_git_flow.md または supplement/git/ に移す
