---
title: "[2024年8月]SpringBootで作ったWEBアプリをRenderで無料で公開する"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [java, spring, render]
published: true
---

springで作ったWEBアプリを無料で公開する方法を紹介します。今まで無料だったHerokuが有料になってしまったので、Renderを使います。

# Spring Bootでアプリを作る

今回は画面を表示するだけの非常に簡単なアプリを用意します。Mavenプロジェクトとして作成します。

参考: [Spring BootでMavenプロジェクトを作成](https://zenn.dev/dollaga_saiore/articles/spring_intelij_maven20240815)

`src/main/resources/static` 配下に `index.html` ファイルを作成しただけのものです。

# Dockerファイルを作る

## ファイル作成

プロジェクト直下にDockerfileを作成します。
![](https://storage.googleapis.com/zenn-user-upload/d387f1376bfa-20240815.png)

## Dockerfileの内容

```docker:Dorkerfile
FROM maven:3-eclipse-temurin-17 AS build
COPY ./ /home/app
RUN cd /home/app && mvn clean package -Dmaven.test.skip=true
FROM eclipse-temurin:17-alpine
COPY --from=build /home/app/target/spring-practice-0.0.1-SNAPSHOT.jar /usr/local/lib/demo.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/usr/local/lib/demo.jar"]
```

### 1. `FROM maven:3-eclipse-temurin-17 AS build`
- **説明**: これはマルチステージビルドの最初のステージです。`maven:3-eclipse-temurin-17`というベースイメージを使用します。このイメージには、Maven 3とJava 17（Temurinディストリビューション）がインストールされています。
- **役割**: このステージはアプリケーションをビルドするために使用されます。`AS build`は、このステージに名前（`build`）を付けて、後で参照できるようにします。

### 2. `COPY ./ /home/app`
- **説明**: Dockerコンテナ内の`/home/app`ディレクトリに、ホストマシンのカレントディレクトリ（`./`）の内容をコピーします。
- **役割**: アプリケーションのソースコードと関連ファイルを、ビルドステージのコンテナ内にコピーします。

### 3. `RUN cd /home/app && mvn clean package -Dmaven.test.skip=true`
- **説明**: `/home/app`ディレクトリに移動して、`mvn clean package`コマンドを実行します。`-Dmaven.test.skip=true`はテストの実行をスキップするオプションです。
- **役割**: ソースコードをビルドし、実行可能なJARファイルを生成します。この場合、`target`ディレクトリに`spring-practice-0.0.1-SNAPSHOT.jar`というファイルが生成されます。

### 4. `FROM eclipse-temurin:17-alpine`
- **説明**: マルチステージビルドの2つ目のステージです。`eclipse-temurin:17-alpine`という軽量なJava 17のベースイメージを使用します（Alpine Linuxベース）。
- **役割**: このステージは、実行時に必要な軽量なJavaランタイム環境を提供します。ビルドに不要なMavenは含まれません。

### 5. `COPY --from=build /home/app/target/spring-practice-0.0.1-SNAPSHOT.jar /usr/local/lib/demo.jar`
- **説明**: 最初のステージ（`build`）から、ビルド済みのJARファイルをコピーします。コピー元は`/home/app/target/spring-practice-0.0.1-SNAPSHOT.jar`、コピー先は新しいコンテナ内の`/usr/local/lib/demo.jar`です。
- **役割**: ビルドされたJARファイルを実行ステージのコンテナにコピーします。これで、アプリケーションが実行可能になります。

### 6. `EXPOSE 8080`
- **説明**: Dockerコンテナが使用するポートを指定します。この場合、アプリケーションはポート8080でリッスンします。
- **役割**: このステートメント自体はポートを開くことはしませんが、コンテナがこのポートを使用することを他の開発者やオーケストレーションツールに伝えます。

### 7. `ENTRYPOINT ["java", "-jar", "/usr/local/lib/demo.jar"]`
- **説明**: コンテナが起動したときに実行されるコマンドを指定します。この場合、`java -jar /usr/local/lib/demo.jar`を実行して、Spring Bootアプリケーションを起動します。
- **役割**: コンテナのエントリーポイントを設定し、指定されたコマンドが実行されるようにします。


spring-practice-0.0.1-SNAPSHOT.jarの値は、pom.xmlファイルから値を参照してください。
```:pom.xml
	<artifactId>spring-practice</artifactId>
	<version>0.0.1-SNAPSHOT</version>
```

ここでは以下の値をハイフンで繋いで入力しています。
<artifactId>spring-practice</artifactId>
<version>0.0.1-SNAPSHOT</version>


# GitHubに登録
詳細な手順は以下を参照してください:
https://pleiades.io/help/idea/github.html


# Renderにデプロイ

1. [Render](https://render.com/)を開きます。

2. **Get Started or Free**をクリックします。  
   ![](https://storage.googleapis.com/zenn-user-upload/4a0d8341ba5f-20240814.png)

3. **Create an account**の**GitHub**をクリックして連携します。  
   ![](https://storage.googleapis.com/zenn-user-upload/95e8e2a42b17-20240814.png)

4. ダッシュボードが開いたら、**＋New**を押して、**Web Service**を選択します。  
   ![](https://storage.googleapis.com/zenn-user-upload/053243902863-20240814.png)

5. **Public Git Repository**を選択し、GitHubからアプリのURLをコピーして貼り付けます。  
   **Connect**をクリックします。  
   ![](https://storage.googleapis.com/zenn-user-upload/d32fd9dd9b37-20240814.png)

   ※URLは、GitHubで該当のWEBアプリの**Code** → **HTTPS**からコピーできます。  
   ![](https://storage.googleapis.com/zenn-user-upload/02080301ef69-20240814.png)

6. **Instance Type**で**Free**を選択します。  
   ![](https://storage.googleapis.com/zenn-user-upload/b005763341be-20240814.png)

7. **Deploy Web Service**をクリックします。  
   ![](https://storage.googleapis.com/zenn-user-upload/ef7909066270-20240814.png)

8. デプロイが開始します。  
   ![](https://storage.googleapis.com/zenn-user-upload/ba6491c3d068-20240814.png)

   進行状況が**In Progress**から**Live**になったら完了です。URLが表示されるので、クリックしてアクセスします。  
   ![](https://storage.googleapis.com/zenn-user-upload/e56caf438bf3-20240814.png)

9. 表示を確認します。  
   ![](https://storage.googleapis.com/zenn-user-upload/cf1c764a3f0c-20240814.png)

# アプリケーションの停止、削除

1. `Dashboard`のタブから該当のアプリの[…]をクリックして、**Settings**を選択します。  
   ![](https://storage.googleapis.com/zenn-user-upload/96f4a80314d9-20240815.png)

2. 画面を下にスクロールし、以下のオプションを選択します:
   - 削除したい場合は**Delete Web Service**。
   - 一時的に停止したい場合は**Suspend Web Service**。  
   ![](https://storage.googleapis.com/zenn-user-upload/c5649d712083-20240815.png)

# 参考

https://qiita.com/hardreggaecafe/items/406042168fe2bce3b53d
