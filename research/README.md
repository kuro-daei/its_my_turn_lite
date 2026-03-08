# research/ — 調査結果の保存フォルダ

このフォルダは **researcher エージェント** が調査結果を保存する場所です。

## 命名規則

```
YYYY-MM-DD-<トピック>.md
```

例：
- `2026-03-08-rust-ownership.md`
- `2026-03-09-github-actions.md`

## ファイル構成テンプレート

```markdown
# 調査: <トピック>

調査日: YYYY-MM-DD
調査者: researcher エージェント

## 要約

（2〜3行で概要を記述）

## 詳細

（調査内容の詳細）

## 情報源

- [リンクテキスト](URL)
```

## ワークフロー

```
researcher → research/<日付>-<トピック>.md に保存
                   ↓
writer     → このファイルを読んで文書を作成
                   ↓
reviewer   → 完成文書の品質をチェック
```

> **注意:** このフォルダの内容は下書きです。
> 公開したくない場合は `.gitignore` に `research/` を追加してください。
