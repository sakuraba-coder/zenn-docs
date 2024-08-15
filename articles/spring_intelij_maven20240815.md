---
title: "[2024年8月] IntelliJでSpring Bootアプリを簡単に作り始める方法"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [springBoot, intellij, java, springInitializr]
published: true
---

IntelliJ IDEA Communityを使って、Springプロジェクトの環境を簡単に整える方法を紹介します。

# IntelliJ IDEAをインストール
IntelliJ IDEAは、現在世界的に人気のあるJavaの統合開発環境です。日本ではEclipseのシェアが高いですが、世界的にはIntelliJ IDEAが圧倒的に人気です。

ダウンロードはこちらから:
[IntelliJ IDEA ダウンロード](https://www.jetbrains.com/ja-jp/idea/download/?section=windows)

IntelliJ IDEAには、有料版の*IntelliJ IDEA Ultimate*と無償版の*IntelliJ IDEA Community版*があります。今回は無償版を使用します。Springを本格的に開発する場合は、可能であれば有料版の使用を検討するのも良いでしょう。
詳細はこちら:
[IntelliJ IDEA UltimateのSpringサポート](https://www.jetbrains.com/ja-jp/idea/spring/#intelligent-coding-assistance)

:::message
IntelliJ IDEAを使用するにはJDKが必要です。まだインストールしていない方は、先にJDKをダウンロードしてください。
[JDKダウンロード](https://www.oracle.com/jp/java/technologies/downloads/#jdk22-windows)
:::

# Spring Initializrでひな形を作成
## Spring Initializrとは？
Spring Initializrは、ビルドツールや開発言語を選択することで、簡単に雛形のアプリケーションを生成できるツールです。Web上で利用できるため、特別なソフトウェアのインストールは不要です。

Spring Initializr:
[https://start.spring.io/](https://start.spring.io/)

## 使い方

![](https://storage.googleapis.com/zenn-user-upload/096c7f87b602-20240815.png)

![](https://storage.googleapis.com/zenn-user-upload/16e093f10cc1-20240815.png)

依存関係は必要最低限のものだけを追加します。今回追加するのは以下の3点です:
- Spring Boot Dev Tools
- Spring Web
- Thymeleaf

![](https://storage.googleapis.com/zenn-user-upload/a79e478797d6-20240815.png)

最後に「Generate」をクリックすると、zipファイルがダウンロードされます。
![](https://storage.googleapis.com/zenn-user-upload/23c3a005c0d9-20240815.png)

:::message
ファイル名は*Project Metadata*の*Artifact*に指定した名前になります。
:::
ダウンロードしたファイルの中身はこのような感じです。
![](https://storage.googleapis.com/zenn-user-upload/54273914b590-20240815.png)

# IntelliJ IDEAで開いて簡単なアプリを作成

IntelliJ IDEAを起動して、[File]→[Open]から先ほどダウンロードして展開したフォルダを指定してください。その際、「このプロジェクトを信頼しますか？」と聞かれますので、「信頼します (Trust Project)」を押してください。

pom.xmlを読み込み、依存するライブラリを自動でインストールしてくれます。
![](https://storage.googleapis.com/zenn-user-upload/37c21ab2546a-20240815.png)

## デフォルトページの作成

`src/main/resources/static` 配下に `index.html` ファイルを作成します。`static` フォルダ上で右クリックし、「New」→「HTMLファイル」を選択します。`index.html`と入力し、Enterを押下します。

ひな形が作成されますので、以下のように修正します。
```html:index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>top page</title>
</head>
<body>
  <p>Hello, world!</p>
  <a href="hello/index">入力ページへ</a>
</body>
</html>
```
## アプリケーションの起動
### プログラムの実行
`DemoApplication.java`を開き、実行します。
Springアプリケーションが起動します。
コンソールに`Started DemoApplication`と出力されたらOKです。
![](https://storage.googleapis.com/zenn-user-upload/bc2593e722ff-20240815.png)

:::message
以下のように警告が出ることがあります。`アクセスを許可する`を押下してください。
:::
![](https://storage.googleapis.com/zenn-user-upload/a5aec26e33d4-20240815.png)

### 画面の表示
http://localhost:8080/index.html にアクセスすると、以下のように表示されます。
![](https://storage.googleapis.com/zenn-user-upload/9334b5f611d3-20240815.png)



# まとめ
Springの開発環境はいろいろありますが、IntelliJ IDEAとSpring Initializrの組み合わせが一番簡単かなと思います。

# 参考
https://qiita.com/TaikiTkwkbysh/items/5553a8625ff95314a392

https://qiita.com/morioheisei/items/5b75da7af8de77d9f1b5