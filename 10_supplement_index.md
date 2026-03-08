# 補足資料一覧

本編（01〜12）で紹介しきれなかった発展的なトピックをまとめた補足資料です。
本編の学習が一通り終わってから参照することを推奨します。

---

## Claude 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/claude/01_mcp.md](./supplement/claude/01_mcp.md) | MCP（Model Context Protocol）と外部ツール連携 | [02_claude_usage.md](./02_claude_usage.md) |
| [supplement/claude/02_cli.md](./supplement/claude/02_cli.md) | Claude CLI 操作リファレンス（ワンショットモード・起動オプション） | [02_claude_usage.md](./02_claude_usage.md) |
| [supplement/claude/03_git_flow.md](./supplement/claude/03_git_flow.md) | Claude と Git の連携フロー詳細 | [08_github_publish.md](./08_github_publish.md)・[05_git_internals.md](./05_git_internals.md) |

## Git 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) | Git の内部しくみ詳細（.git構造・オブジェクトモデル・git diff・git log） | [05_git_internals.md](./05_git_internals.md) |
| [supplement/git/02_conflict.md](./supplement/git/02_conflict.md) | マージとコンフリクト解決 | [11_pull_request.md](./11_pull_request.md)・[12_conflict.md](./12_conflict.md) |
| [supplement/git/03_team_workflow.md](./supplement/git/03_team_workflow.md) | チーム開発ワークフロー（複数人協働演習） | [10_branch_strategy.md](./10_branch_strategy.md)・[11_pull_request.md](./11_pull_request.md) |
| [supplement/git/04_command_reference.md](./supplement/git/04_command_reference.md) | Git・GitHub コマンドリファレンス早見表 | 本編全般 |

---

## 本編の学習フロー

補足資料に進む前に、以下の本編を順に進めてください：

```
【フェーズ1: ソロ × 博報堂】
00_preparation → 01_bash_basics → 02_claude_usage → 03_agent_workflow
→ 04_research_with_claude → 05_git_internals

【フェーズ2: チーム × Apple】
06_team_invite → 07_research_team → 08_github_publish
→ 09_markdown → 10_branch_strategy → 11_pull_request → 12_conflict
```
