---
title: "[2024年8月]virtualboxでwindowsサーバーを立てる"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [仮想化, virtualbox, windowsサーバー]
published: true
---

このページではvirtualboxからwindowsサーバーを起動する方法を紹介します。
結構詰まった部分もあったので、備忘録がてら書きます。

```環境
Windows 10 Pro
プロセッサ	Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz   1.99 GHz
実装 RAM	32.0 GB (31.9 GB 使用可能)
システムの種類	64 ビット オペレーティング システム、x64 ベース プロセッサ
```

# virtualBoxとwindowsサーバーのイメージファイルをダウンロード

イメージファイルをただの画像ファイルだと思っていたのは僕だけではないはず
とりあえず必要なものをそれぞれダウンロードしましょう
容量がかなり大きいためダウンロードは結構時間かかります

インストールはすぐなので、まずは必要なものをダウンロードしましょう

## virtualboxをダウンロード
以下のサイトにアクセス
https://www.oracle.com/jp/virtualization/technologies/vm/downloads/virtualbox-downloads.html

まずは以下からvirtualBoxのインストーラーをダウンロード
![virtualboxダウンロード場所](https://storage.googleapis.com/zenn-user-upload/cf281305c33f-20240812.png)

Extension Packもついでにダウンロード
![virtualboxExダウンロード場所](https://storage.googleapis.com/zenn-user-upload/2a232cc56704-20240812.png)


## windowsサーバーのイメージをダウンロード
Windows Server 2022をインストールに利用するISOファイルについては、180日間利用できる評価版がマイクロソフトのEvalution Centerからダウンロードできます。

https://www.microsoft.com/ja-jp/evalcenter/download-windows-server-2022

日本語バージョンをここではダウンロードしておきます
![](https://storage.googleapis.com/zenn-user-upload/cb830c682f4e-20240812.png)


ここまでで、以下のようにダウンロード出来ているかと思います。
![](https://storage.googleapis.com/zenn-user-upload/52ff4934133c-20240812.png)

# virtualBoxのインストール
さきほどダウンロードした*VirtualBox-7.0.20-163906-Win.exe*を実行
そのまま初期設定でOK押しまくってインストール。

それができたらExtension_Packの方も実行してインストール。全部OKで大丈夫。細かいことはあとから変えればいい。

# windowsサーバーの起動
ショートカットがデスクトップに出来ているはずなので、そこから起動。ウィンドウ上部にある新規ボタンをクリック

![](https://storage.googleapis.com/zenn-user-upload/0780e648ae95-20240812.png)


「名前」に仮想マシンの名前を付けて、「ISOイメージ」には右のボタンを押して先ほどダウンロードしたISOファイルを選択する。「次へ」を押下。
![](https://storage.googleapis.com/zenn-user-upload/059d544a9451-20240812.png)

ユーザー名とパスワードを必ず変更し、「次へ」を押下
ホスト名などは変更しなくてよい。
![](https://storage.googleapis.com/zenn-user-upload/7e69745f7295-20240813.png)

メモリを指定する。デフォルトでは2048MBだが、４096MB以上にすることをオススメ。
![](https://storage.googleapis.com/zenn-user-upload/0572bde9dc8d-20240813.png)

ハードディスクの指定。windowsサーバーの場合、linuxサーバよりは容量が大きくなる。ここはデフォルトの50Gで指定。
![](https://storage.googleapis.com/zenn-user-upload/14b24bbe9d00-20240813.png)

確認。そのまま「完了」でよい。
![](https://storage.googleapis.com/zenn-user-upload/67bff9907de0-20240813.png)

無事に出来ると以下の画像のように追加されている。
![](https://storage.googleapis.com/zenn-user-upload/be489404acfb-20240813.png)

作成した仮想マシンを指定し「起動」ボタンを押下で起動するはず。。。

## 起動できないエラー
しかし、なんど起動ボタンを押しても画面が出そうになるも落ちる、を繰り返す

なんらかのエラーが出ているため調査

```
VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED).
終了コード : E_FAIL (0x80004005)
```

何これBIOS？なんの関係が。。。と思って調べてみると、BIOSが仮想化を無効にしているとのこと。
https://qiita.com/kohei-takao/items/203585d62dc393789b80

というわけで再起動してF2連打でBIOSを起動
*Intel Virtualization Technology*を*Disabled* → *Enabled*に変更!!

BIOSは機器によって違う。設定項目の中の「*Intel Virtualization Technology*」を探す。
必ず見つかるはず。

設定を変更してOK。windowsを起動する。

## 無事に起動
とりあえずwindowsサーバ画面が起動。よかったよかった。
![](https://storage.googleapis.com/zenn-user-upload/5e5ff5c9f2f2-20240813.png)

## 日本語キーボードになってない。
キーボードが英語配列になっており、日本語キーボードに設定しようとしてもそもそも設定できなかった。（やり方はあるかもしれない。）

何か記号などを入力したい場合にはソフトキーボードを活用しましょう。
「入力」→「キーボード」→「ソフトキーボード」
![](https://storage.googleapis.com/zenn-user-upload/1f75aaf919c4-20240813.png)

![](https://storage.googleapis.com/zenn-user-upload/d8ad91463bb0-20240813.png)

# まとめ
環境構築はエラーとの闘いですが、（日本語）情報が少なくて困りました。
あんまりサクサク動かないのはメモリ不足かな？パソコンを買い替えてサクサク動くようにしたいですね。サクサク。

# 参考
https://www.rem-system.com/winsvr2022-install/
