---
title: "Zennの書き方ガイド"
emoji: "✍️"
type: "tech"
topics: [Zenn, Markdown, VSCode, GitHub]
published: false
---

# Zennの書き方

## 🛠 必要なツール
Zennの記事を書くためには、以下のツールを準備しましょう。

- **VSCode**（Visual Studio Code）
- **Node.js**（npxコマンドを使用するため）
- **GitHubアカウント**（記事管理用）

## 📥 GitHubからクローン
まず、ZennのドキュメントをGitHubからクローンします。

![GitHubのクローンのURL](https://storage.googleapis.com/zenn-user-upload/f4bd20467113-20250320.png)

![VSCodeの最初の画面](https://storage.googleapis.com/zenn-user-upload/86a6ab2f6b9f-20250320.png)

クローンするフォルダは以下を選択します。

```
C:\development\zenn
```

または、以下のコマンドを実行します。

```sh
cd C:\development\zenn\
git clone https://github.com/sakuraba-coder/zenn-docs.git
cd zenn-docs
```

これでローカル環境にリポジトリが準備されます。

## ✍️ Zenn記事の作成
Zenn CLIを使って、新しい記事を作成します。

```sh
npx zenn new:article --slug "my-first-article" --title "My First Article"
```

このコマンドを実行すると、`articles/my-first-article.md` というMarkdownファイルが作成されます。

## 🚀 ローカルでプレビュー
記事を編集したら、ローカルでプレビューできます。

```sh
npx zenn preview
```

このコマンドを実行すると、`http://localhost:8000` で記事のプレビューが可能になります。

---

