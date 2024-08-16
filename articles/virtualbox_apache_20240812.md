---
title: "[2024年8月] VirtualBoxにWindowsサーバーを立ててApacheをインストールする"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [VirtualBox, Windowsサーバー, Apache]
published: true
---

このページでは、VirtualBoxに立てたWindowsサーバーにApacheを導入する方法を紹介します。

## VirtualBoxの仮想サーバを起動
VirtualBoxにWindowsサーバーを導入する方法については、以下の記事をご参照ください。  
[VirtualBoxにWindowsサーバーを導入する方法](https://zenn.dev/dollaga_saiore/articles/virtualbox_windows_20240812)

## Apacheのインストール手順

ここからは、仮想環境内での操作を行います。

1. [Lhaplus](https://forest.watch.impress.co.jp/library/software/lhaplus/) のインストール
2. [Visual C++ 再頒布可能パッケージ](https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist?view=msvc-170) のダウンロード
3. Visual C++ 再頒布可能パッケージのインストール
4. Apache 2.4 のダウンロード
5. Apache 2.4 のインストール

### 1. Lhaplusのインストール
初期状態では、ZIPファイルを解凍できないため、Lhaplusをインストールします。もちろん、7-Zipなど他の解凍ツールでも構いません。  
[Lhaplus ダウンロードリンク](https://forest.watch.impress.co.jp/library/software/lhaplus/)

1. ダウンロードした「lpls174.exe」を実行します。

    ![](https://storage.googleapis.com/zenn-user-upload/9cce0c7585c6-20240813.png)

2. インストーラーが文字化けしていますが、「次へ」をクリックします。

    ![](https://storage.googleapis.com/zenn-user-upload/3c82afee7a27-20240813.png)

3. そのまま「次へ」をクリックします。

    ![](https://storage.googleapis.com/zenn-user-upload/e460105fc457-20240813.png)

4. 「はい」をクリックしてインストールを進めます。

    ![](https://storage.googleapis.com/zenn-user-upload/3d8cf1f4e8f6-20240813.png)

5. インストール完了後、「完了」をクリックします。

    ![](https://storage.googleapis.com/zenn-user-upload/5eb8d5028cfd-20240813.png)

これでZIPファイルが解凍できるようになりました。

### 2. Visual C++ 再頒布可能パッケージのダウンロード
Apacheを実行するには*Visual C++ 再頒布可能パッケージ*が必要です。以下のリンクからダウンロードしてください。  
[Visual C++ 再頒布可能パッケージ ダウンロードリンク](https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist?view=msvc-170)

1. 64bit版をダウンロードします。

    ![](https://storage.googleapis.com/zenn-user-upload/ef6ef77f67ce-20240813.png)

2. ダウンロードした「vc_redist.x64.exe」を確認します。

    ![](https://storage.googleapis.com/zenn-user-upload/0e3b78f74b5a-20240813.png)

### 3. Visual C++ 再頒布可能パッケージのインストール
1. ダウンロードした「vc_redist.x64.exe」を実行し、「インストール」をクリックします。

    ![](https://storage.googleapis.com/zenn-user-upload/ecff62992209-20240813.png)

これでVisual C++のインストールが完了しました。

### 4. Apache 2.4のダウンロード
以下のサイトからApache 2.4をダウンロードします。  
[Apache Lounge ダウンロードリンク](https://www.apachelounge.com/download/)

1. 「httpd-2.4.62-240718-win64-VS17.zip」をダウンロードします。

    ![](https://storage.googleapis.com/zenn-user-upload/aa1552668343-20240813.png)

2. ダウンロードフォルダに格納されていることを確認します。

    ![](https://storage.googleapis.com/zenn-user-upload/597d05348da8-20240813.png)

### 5. Apache 2.4のインストール

#### ファイルの展開
1. ダウンロードした「httpd-2.4.62-240718-win64-VS17.zip」を解凍します。展開後、「Apache24」フォルダが表示されます。

    ![](https://storage.googleapis.com/zenn-user-upload/5187cb1a3eea-20240813.png)

2. このフォルダをCドライブ直下に移動します。

    ![](https://storage.googleapis.com/zenn-user-upload/9040fc94ad36-20240813.png)

3. 「Apache24」フォルダの内容を確認します。

    ![](https://storage.googleapis.com/zenn-user-upload/bbd7173871cc-20240813.png)

主なフォルダの内容は以下の通りです：
- **bin**: 実行ファイルが含まれています
- **conf**: 設定ファイルが含まれています
- **htdocs**: HTMLファイルなどの静的コンテンツが含まれています
- **logs**: エラーログなどのログファイルが含まれています

#### httpdコマンドでインストール
1. コマンドプロンプトを管理者権限で実行します。

2. Apacheの`bin`フォルダに移動して、以下のコマンドを実行します。

    ```shell
    cd c:\Apache24\bin
    httpd -k install
    ```

    ![](https://storage.googleapis.com/zenn-user-upload/3015e4166d9c-20240813.png)

    「service is successfully installed.」と表示されたら、インストールが成功しています。

#### Apache 2.4の起動
1. コマンドプロンプトで、以下のコマンドを使用してApacheを起動します。

    ```shell
    net start apache2.4
    ```

    Apacheを停止するには、以下のコマンドを使用します。

    ```shell
    net stop apache2.4
    ```

    ![](https://storage.googleapis.com/zenn-user-upload/cc92ebe2b67c-20240813.png)

2. ブラウザで`http://localhost`にアクセスし、「It Works!」が表示されれば、Apacheが正常に動作していることを確認できます。

    ![](https://storage.googleapis.com/zenn-user-upload/5a97a5d4772f-20240813.png)

以上で、Apacheのインストールと動作確認が完了しました。エラーが発生した場合は、`logs`フォルダ内のエラーログを確認してください。
