# 補足資料一覧

本編（04〜15）で紹介しきれなかった発展的なトピックをまとめた補足資料です。
本編の学習が一通り終わってから参照することを推奨します。

---

## Claude 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/claude/01_mcp.md](./supplement/claude/01_mcp.md) | MCP（Model Context Protocol）と外部ツール連携 | [06_claude_usage.md](./06_claude_usage.md) |
| [supplement/claude/02_cli.md](./supplement/claude/02_cli.md) | Claude CLI 操作リファレンス（ワンショットモード・起動オプション） | [06_claude_usage.md](./06_claude_usage.md) |
| [supplement/claude/03_git_flow.md](./supplement/claude/03_git_flow.md) | Claude と Git の連携フロー詳細 | [10_research_team.md](./10_research_team.md)・[08_git_internals.md](./08_git_internals.md) |

## Git 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) | Git の内部しくみ詳細（.git構造・オブジェクトモデル・git diff・git log） | [08_git_internals.md](./08_git_internals.md) |
| [supplement/git/02_conflict.md](./supplement/git/02_conflict.md) | マージとコンフリクト解決 | [12_pull_request.md](./12_pull_request.md)・[13_conflict.md](./13_conflict.md) |
| [supplement/git/03_team_workflow.md](./supplement/git/03_team_workflow.md) | チーム開発ワークフロー（複数人協働演習） | [11_branch_strategy.md](./11_branch_strategy.md)・[12_pull_request.md](./12_pull_request.md) |
| [supplement/git/04_command_reference.md](./supplement/git/04_command_reference.md) | Git・GitHub コマンドリファレンス早見表 | 本編全般 |

---

## 本編の学習フロー

補足資料に進む前に、以下の本編を順に進めてください：

```
【フェーズ1: ソロ × 博報堂】
02_preparation_mac / 03_preparation_windows → 04_bash_basics → 06_claude_usage → 07_agent_workflow
→ 08_git_internals

【フェーズ2: チーム × Apple】
09_team_invite → 10_research_team → 10_research_team
→ 05_markdown → 11_branch_strategy → 12_pull_request → 13_conflict
```
