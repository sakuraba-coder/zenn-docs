---
title: "Springにおけるパスの指定方法まとめ"
emoji: "🐥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Java, Spring]
published: true
---

# Spring MVC でのパス指定方法のまとめ

## はじめに

Spring MVC では、コントローラーメソッドやビューのパス指定にさまざまな方法があります。この記事では、主なパス指定方法とそれらの使い方についてまとめます。

## 1. `@RequestMapping`アノテーション

`@RequestMapping`アノテーションを使用してクラスやメソッドにパスを指定する方法です。

### 1.1 クラスレベルの指定

```java
@Controller
@RequestMapping("/example")
public class ExampleController {
    // ...
}
```

### 1.2 メソッドレベルの指定

```java
@Controller
@RequestMapping("/example")
public class ExampleController {

    @GetMapping("/path")
    public String examplePath() {
        // ...
    }
}
```

## 2. `redirect:`を使用したリダイレクト

`redirect:`を使用してリダイレクト先のパスを指定する方法です。

```java
@Controller
@RequestMapping("/example")
public class ExampleController {

    @GetMapping("/original")
    public String originalPage() {
        return "original";
    }

    @GetMapping("/redirect-to-original")
    public String redirectToOriginal() {
        return "redirect:/example/original";
    }
}
```

この場合、`/redirect-to-original`へのアクセスは、`originalPage`メソッドを経由して`original`テンプレートにリダイレクトされます。

## 3. 絶対パスと相対パスの違い

Spring MVC では、絶対パスと相対パスが異なる挙動を示すことがあります。

### 3.1 絶対パスの指定

```java
@Controller
@RequestMapping("/example")
public class ExampleController {

    @GetMapping("/absolute")
    public String absolutePath() {
        return "redirect:/example/original";
    }
}
```

- URL: `http://localhost:8080/example/original`

### 3.2 相対パスの指定

```java
@Controller
@RequestMapping("/example")
public class ExampleController {

    @GetMapping("/relative")
    public String relativePath() {
        return "redirect:original";
    }
}
```

- URL: `http://localhost:8080/example/relative/original`

## おわりに

Spring MVC において、適切なパス指定方法を選択することはアプリケーションの正確な動作に重要です。異なる状況に対応するために、各パス指定方法の特徴や使い方を理解しておくと良いでしょう。
