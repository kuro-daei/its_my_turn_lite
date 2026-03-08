# supplement/07. Claude CLI 操作リファレンス

Claude Code の高度なコマンドライン操作をまとめた補足資料です。
[02_claude_usage.md](../../02_claude_usage.md) の基本操作を習得した後に参照してください。

---

## 目次

1. [ワンショットモード（対話なし実行）](#1-ワンショットモード対話なし実行)
2. [起動オプション一覧](#2-起動オプション一覧)
3. [環境変数・設定](#3-環境変数設定)

---

## 1. ワンショットモード（対話なし実行）

通常の `claude` コマンドは対話モードで起動しますが、
`-p` フラグを使うと 1 回の指示だけを実行して終了させることができます。

```bash
# -p フラグで指示を渡す
claude -p "このフォルダのファイル一覧を教えて"

# 出力形式をテキストに指定（デフォルトはリッチ表示）
claude -p "README.md を要約して" --output-format text

# JSON 形式で受け取る（自動化スクリプト向け）
claude -p "README.md の内容を要約して" --output-format json
```

### 活用場面

シェルスクリプトや定期実行ジョブに組み込むと便利です。

```bash
# 例: 毎朝 notes/ の内容を一行サマリーする
claude -p "notes/ フォルダ内のファイルを一覧して、各ファイルの内容を1行で要約して" \
  --output-format text
```

> **注意:** ワンショットモードは対話できないため、複雑な作業よりも
> 「確認」「要約」「一覧取得」など単純なタスクに向いています。

---

## 2. 起動オプション一覧

| オプション | 説明 | 例 |
|---|---|---|
| `claude` | 対話モードで起動 | `claude` |
| `claude --plan` | プランモードで起動 | `claude --plan` |
| `claude -p "..."` | ワンショットモード | `claude -p "README を要約して"` |
| `--output-format text` | テキスト形式で出力 | `claude -p "..." --output-format text` |
| `--output-format json` | JSON 形式で出力 | `claude -p "..." --output-format json` |
| `--model <モデル名>` | 使用モデルを指定 | `claude --model claude-haiku-4-5` |
| `--version` | バージョンを表示して終了 | `claude --version` |

---

## 3. 環境変数・設定

### API キー認証

ブラウザを開かずに API キーで認証する方法です。

```bash
# .bashrc や .zshrc に追加
export ANTHROPIC_API_KEY="sk-ant-..."
```

設定後はターミナルを再起動（または `source ~/.bashrc`）してから `claude` を起動してください。

> **セキュリティ注意:** API キーはコードやリポジトリにコミットしないでください。
> `.bashrc` に書く場合、そのファイルが Git 管理下に入っていないことを確認してください。

### モデルのデフォルト設定

よく使うモデルをデフォルトにするには、`/model` コマンドで設定するか、
起動時に `--model` オプションで指定します。

```bash
claude --model claude-haiku-4-5    # 高速・低コストモデルで起動
claude --model claude-opus-4-6     # 高性能モデルで起動
```

---

→ [補足資料一覧](../../10_supplement_index.md)
