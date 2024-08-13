---
title: "2024年8月[VirtualBox]windowsサーバーでApacheを立てる"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [VirtualBox, windowsサーバー, Apache]
published: true
---

このページではVirtualBoxに立てたWindowsサーバーにApacheを導入する方法を紹介します。

# VirtualBoxの仮想サーバを起動
VirtualBoxにWindowsサーバーを導入する方法は前の記事で行いました。
https://zenn.dev/dollaga_saiore/articles/virtualbox_windows_20240812


# Apacheのダウンロード

ここからは起動した仮想環境の中で操作します。
1. [Lhaplus](https://forest.watch.impress.co.jp/library/software/lhaplus/)のインストール
2. [Visual C++ 再頒布可能パッケージ](https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist?view=msvc-170)のダウンロード
3. Visual C++ 再頒布可能パッケージのインストール
4. Apache2.4のダウンロード
5. Apache2.4のインストール

## Lhaplusのインストール
初期状態だとダウンロードしても解凍ができないので、Lhaplusをダウンロードしておく。もちろん7zipでもなんでもいい。
https://forest.watch.impress.co.jp/library/software/lhaplus/

![](https://storage.googleapis.com/zenn-user-upload/22b219772980-20240813.png)

ダウンロードした「lpls174.exe」を実行
![](https://storage.googleapis.com/zenn-user-upload/9cce0c7585c6-20240813.png)

文字化けしてますが読めますね、「次へ」です
![](https://storage.googleapis.com/zenn-user-upload/3c82afee7a27-20240813.png)

そのまま「次へ」
![](https://storage.googleapis.com/zenn-user-upload/e460105fc457-20240813.png)

なぜか読めます。「はい」です。
![](https://storage.googleapis.com/zenn-user-upload/3d8cf1f4e8f6-20240813.png)

「完了」です。
![](https://storage.googleapis.com/zenn-user-upload/5eb8d5028cfd-20240813.png)

インストールされますので、終了します。
![](https://storage.googleapis.com/zenn-user-upload/7269bf9b7e51-20240813.png)

これでzipファイルが解凍できるようになりました。

## Visual C++ 再頒布可能パッケージのダウンロード
結構最初に躓きがちなのですが、Apatcheの実行には*Visual C++ 再頒布可能パッケージ*が必要なので事前にインストールしておきます。
以下からダウンロードします。
https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist?view=msvc-170

![](https://storage.googleapis.com/zenn-user-upload/f2429efd43ec-20240813.png)

64bit版をダウンロードします。
![](https://storage.googleapis.com/zenn-user-upload/ef6ef77f67ce-20240813.png)

vc_redist.x64.exeがダウンロードされていればOKです
![](https://storage.googleapis.com/zenn-user-upload/0e3b78f74b5a-20240813.png)

## Visual C++ 再頒布可能パッケージのインストール
vc_redist.x64.exeを実行し、「インストール」を押下
![](https://storage.googleapis.com/zenn-user-upload/ecff62992209-20240813.png)

以上です。

## Apache2.4のダウンロード
参考: https://www.javadrive.jp/apache/install/index1.html

以下のサイトからダウンロードします
https://www.apachelounge.com/download/

*httpd-2.4.62-240718-win64-VS17.zip*を選択しダウンロード
![](https://storage.googleapis.com/zenn-user-upload/aa1552668343-20240813.png)

ダウンロードフォルダに格納される
![](https://storage.googleapis.com/zenn-user-upload/597d05348da8-20240813.png)

## Apache2.4のインストール

### ファイルの展開
先ほどダウンロードした*httpd-2.4.62-240718-win64-VS17.zip*を解凍し、展開すると中にApache24を確認できる
![](https://storage.googleapis.com/zenn-user-upload/5187cb1a3eea-20240813.png)

わかりやすくするために、これをCドライブ直下に移動する。コピペでも切り取りでもOK。

![](https://storage.googleapis.com/zenn-user-upload/9040fc94ad36-20240813.png)

Apache24のフォルダの中を覗いてみる。
![](https://storage.googleapis.com/zenn-user-upload/bbd7173871cc-20240813.png)

```
主なフォルダ
- bin 実行ファイル
- conf 設定ファイル
- htdocs HyperTextDocuments HTMLファイルなどの静的コンテンツ
- logs エラーログなどログファイル
```

### httpdコマンドでインストール
ここでコマンドプロンプトを管理者権限で実行する
```
フォルダ移動
$ cd c:\Apache\bin 
httpdコマンドを実行しApacheをインストールする
$ httpd -k install 
```

![](https://storage.googleapis.com/zenn-user-upload/3015e4166d9c-20240813.png)

[service is successfully installed.]が出力されたらOK

https://qiita.com/TechCat56/items/aba26453c10414ce68f6

## Apache2.4の起動
いくつかやりかたはあるが、そのままnetコマンドで起動する

```
起動
$ net start apache2.4 
停止
$ net stop apache2.4
```

![](https://storage.googleapis.com/zenn-user-upload/cc92ebe2b67c-20240813.png)

ブラウザから`http://localhost`にアクセス
htdocs配下(ドキュメントルート)のindex.htmlがレスポンスされ、下記画像のようにIt Works!と表示される。
![](https://storage.googleapis.com/zenn-user-upload/5a97a5d4772f-20240813.png)

https://marycore.jp/coding/htdocs-document-root/

これで動作確認が取れたのでOK！
エラーなどが起こった場合、logs配下のエラーログを確認する。


