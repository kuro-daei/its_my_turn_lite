# 補足資料一覧

本編（01〜09）で紹介しきれなかった発展的なトピックをまとめた補足資料です。
本編の学習が一通り終わってから参照することを推奨します。

---

## Claude 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/claude/01_mcp.md](./supplement/claude/01_mcp.md) | MCP（Model Context Protocol）と外部ツール連携 | [02_claude_usage.md](./02_claude_usage.md) |
| [supplement/claude/02_cli.md](./supplement/claude/02_cli.md) | Claude CLI 操作リファレンス（ワンショットモード・起動オプション） | [02_claude_usage.md](./02_claude_usage.md) |
| [supplement/claude/03_git_flow.md](./supplement/claude/03_git_flow.md) | Claude と Git の連携フロー詳細 | [05_github_publish.md](./05_github_publish.md)・[06_git_internals.md](./06_git_internals.md) |

## Git 関連

| ファイル | 内容 | 推奨する前提知識 |
|---|---|---|
| [supplement/git/01_deep_dive.md](./supplement/git/01_deep_dive.md) | Git の内部しくみ詳細（.git構造・オブジェクトモデル・git diff・git log） | [06_git_internals.md](./06_git_internals.md) |
| [supplement/git/02_conflict.md](./supplement/git/02_conflict.md) | マージとコンフリクト解決 | [09_pull_request.md](./09_pull_request.md) |
| [supplement/git/03_team_workflow.md](./supplement/git/03_team_workflow.md) | チーム開発ワークフロー（複数人協働演習） | [08_branch_strategy.md](./08_branch_strategy.md)・[09_pull_request.md](./09_pull_request.md) |
| [supplement/git/04_command_reference.md](./supplement/git/04_command_reference.md) | Git・GitHub コマンドリファレンス早見表 | 本編全般 |

---

## 本編の学習フロー

補足資料に進む前に、以下の本編を順に進めてください：

```
00_preparation → 01_bash_basics → 02_claude_usage → 03_agent_workflow
→ 04_research_with_claude → 05_github_publish → 06_git_internals
→ 07_markdown → 08_branch_strategy → 09_pull_request
```
