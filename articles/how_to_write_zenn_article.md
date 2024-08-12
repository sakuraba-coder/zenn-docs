---
title: "VScodeでのzennの記事の書き方メモ"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [zenn, linux]
published: true
---

前提としてgithubにzennのリポジトリは作っている状態とする。

# 記事作成の流れ
1. githubからVSCodeに環境をコピー
2. 記事をコマンドで作成
3. コミットする

## githubを使って記事を作成する
参考 https://zenn.dev/eguchi244_dev/articles/github-zenn-linkage-20230501

まずはgithubからローカル環境にコピーする
そのやり方として**クローン**と**プル**がある
とりあえずクローンしとく

:::message
clone：ファイルを全てコピーする
pull：ローカル側とリモート側で、差異がある(更新されている)ファイルを全てコピーする
参考 https://appdev-room.com/web-git-clone-push
:::

```script
 $ git clone リポジトリのURL
```

## 記事作成のコマンド
記事はnpx zennコマンドで作成する
slugはここで決定する。誰かと被るとエラーになるので、末尾に日付などをつける

プレビュー機能はとても便利。

```script:記事作成
 $ npx zenn new:article --slug 英小文字数字と_-でslugを決める --title タイトルは日本語でおｋ 
```

```script:プレビュー
$ npx zenn preview # プレビュー開始
```
zenn-docs配下(articleにcdせずに)でコマンド実行する

:::message
デフォルトでは localhost:8000 で立ち上がります。
npx zenn preview --port 3000 というようにポート番号の指定もできます。
:::


## 記事の編集をする
1. ファイルを編集
2. コミット用コメントを書く
3. コミット
:::message
slugを変更すると記事の内容の変更ができなくなるので注意。
ファイルを作成するときにslugをわかりやすいようにする。
:::
![commit](https://storage.googleapis.com/zenn-user-upload/3e22ef37ffe9-20240812.png)
## 画像の配置
参考　https://zenn.dev/zenn/articles/deploy-github-images

### 画像の配置場所
/imagesディレクトリに配置
もしくは
https://zenn.dev/dashboard/uploader
にアップロード

**アップロードした方が簡単なのでこちらを採用**

### 画像ファイルの制限
- ファイルサイズは 3MB 以内
- 対応する拡張子は .png .jpg .jpeg .gif .webp のみ

### 画像ファイルの参照方法
```
![](/images/example-image1.png)
![](![](https://storage.googleapis.com/zenn-user-upload/example.png))
```
:::message
/images/から始まる絶対パスにする
:::

アップロードした画像のURLを取得したほうが簡単かな


## 記事の消し方
1. ファイルを消してコミット
2. [ダッシュボード](https://zenn.dev/dashboard)から削除する
安全のため、articlesディレクトリからmarkdownファイルを削除しても zenn.dev 上では削除はされません。

## マークダウン方式
https://zenn.dev/zenn/articles/markdown-guide
基本的にはこちらを参考に

良く使うものを以下に記載

```
# 見出し1
## 見出し2
### 見出し3
#### 見出し4
```
:::message
アクセシビリティの観点から見出し2から始める
:::

### プログラムやコマンドの書き方

```
```プログラムのスタート
プログラムの終わり```
```

### リスト
```
- Hello!
- Hola!
  - Bonjour!
  * Hi!
```
- Hello!
- Hola!
  - Bonjour!
  * Hi!

### 番号付きリスト
```
1. First
2. Second
```
1. First
2. Second

### テキストリンク
```
[アンカーテキスト](リンクのURL)
```

### 画像
```
![](https://画像のURL =250x)
```
=250x はサイズを指定している。px単位
（していしなくてもい）
