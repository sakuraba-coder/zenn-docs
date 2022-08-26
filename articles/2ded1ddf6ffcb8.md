---
title: "VSCでのnpmエラーの解決方法"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [VisualStudioCode]
published: true
---

# npm でのインストールでエラーが出る

## やりたいこと

```
npm install node-sass@4.14.1
```

![](https://storage.googleapis.com/zenn-user-upload/cda0160ed967-20220826.png)

## こんな言葉で調べた

```
While resolving: @testing-library/react@13.3.0
```

## 原因？

現環境では、react プロジェクトを npm で作成すると ver18 であるが、ver17 に直して使おうとした

```script:package.json
{
    "react": "17.0.2",
    "react-dom": "17.0.2",
}
```

それが原因かどうかはわからないが、npm であらゆるインストールが失敗するようになった。

## 解決方法

### @testing-library を削除

```script:package.json
    //以下全部削除
    @testing-library/jest-dom: "^5.16.5",
    @testing-library/react: "^13.3.0",
    @testing-library/user-event: "^13.5.0",

```

### package-lock.json と node_modules を削除

右クリック →「削除」
![](https://storage.googleapis.com/zenn-user-upload/12143a50b12e-20220826.png)

### 下記コマンドを実行

```
 $ npm install
```

## 参考資料

https://qiita.com/shiho97797/items/cacb21d09330de56a505
