---
title: "JPAを@DataJpaTestと@DBRiderでテストする方法"
emoji: "⛳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [JPA, Spring, Test, DataJpaTest, DBRider、DBUnit]
published: true
---

# JPAを@DataJpaTestと@DBRiderでテストする方法
Spring BootでのJPAのテストは、アプリケーションのデータアクセス層の品質を確保するために非常に重要です。この記事では、`@DataJpaTest`と`@DBRider`を使用して、JPAリポジトリのテストを効率的に行う方法を紹介します。

## 前提条件
- Spring Bootプロジェクトがセットアップされていること
- JPAとMySQLがプロジェクトに追加されていること
- MySQLはローカルに構築されていること
- JUnit5がテストフレームワークとして設定されていること

# 依存関係の追加
@DataJpaTestと@DBRiderを使用するために、以下の依存関係をpom.xmlに追加します。

## Maven
```xml:pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>spring-data-jpa-sample</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-data-jpa-sample</name>
    <description>spring-data-jpa-sample</description>
    <properties>
        <java.version>17</java.version>
        <junit.version>5.10.2</junit.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-data-jpa-sample</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <version>3.3.2</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.github.database-rider</groupId>
            <artifactId>rider-junit5</artifactId>
            <version>1.41.0</version>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.jupiter</groupId>
                    <artifactId>*</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.junit.platform</groupId>
                    <artifactId>*</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

# @DataJpaTestとは？
@DataJpaTestは、JPAコンポーネント（エンティティ、リポジトリなど）をテストするためのアノテーションです。

使用例
```java: EmployeeRepositoryTest.java
package com.example.persistence.repository;

import com.example.persistence.entity.Employee;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertFalse;

@DataJpaTest
class EmployeeRepositoryTest {

    @Autowired
    private EmployeeRepository employeeRepository;

    @Test
    void testFindByName() {
        List<Employee> employees = employeeRepository.findByName("John Doe");
        assertFalse(employees.isEmpty());
    }
}
```

# @DBRiderとは？
@DBRiderは、テストデータを簡単に管理するためのライブラリです。データセットを使って、テストの前後にデータベースの状態をリセットすることができます。これにより、テストが他のテストの影響を受けないようにできます。

```java:EmployeeRepositoryTest.java
package com.example.persistence.repository;

import com.example.persistence.entity.Employee;
import com.github.database.rider.core.api.configuration.DBUnit;
import com.github.database.rider.core.api.dataset.DataSet;
import com.github.database.rider.junit5.api.DBRider;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@DBRider
@DBUnit(caseSensitiveTableNames = false, schema = "spring", url = "jdbc:mysql://localhost:3306/spring", user = "user", password = "P@ssw0rd")
class EmployeeRepositoryTest {

    @Autowired
    private EmployeeRepository employeeRepository;

    @Test
    @DataSet(value = "initial_dataset.yml")
    void testFindByName() {
        List<Employee> employees = employeeRepository.findByName("John Doe");
        assertEquals(1, employees.size());
    }
}

```

# テスト
## DBを用意

```sql:スキーマ、ユーザー作成
DROP DATABASE IF EXISTS spring;
CREATE DATABASE spring CHARACTER SET utf8mb4;
DROP USER IF EXISTS 'user'@'localhost';
CREATE USER 'user'@'localhost' IDENTIFIED BY 'user';
GRANT ALL PRIVILEGES ON spring.* to 'user'@'localhost';
USE spring;

```

```sql:createTable文
CREATE TABLE employee
(
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(10),
    joined_date DATE,
    department_name VARCHAR(20),
    email VARCHAR(256),
    birth_day DATE
);
```

```sql:データ挿入
INSERT INTO employee VALUES(109, '長谷川宏', '2020-04-01', '開発部', 'hasegawa@example.com', '1990-11-29');
```

## DTO
```java:Employee.java
package com.example.persistence.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;


import java.time.LocalDate;
import java.util.Objects;

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy= GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private LocalDate joinedDate;
    private String departmentName;
    private String email;
    private LocalDate birthDay;

    // Spring Dataがエンティティオブジェクトを生成する時に利用する
    public Employee(){
    }

    // 引数ありのコンストラクタを別途宣言できる
    public Employee(Integer id, String name, LocalDate joinedDate, String departmentName, String email, LocalDate birthDay) {
        this.id = id;
        this.name = name;
        this.joinedDate = joinedDate;
        this.departmentName = departmentName;
        this.email = email;
        this.birthDay = birthDay;
    }

    //getter, setter, toString, equals, hasCodeは省略。

}

```

:::message
  toString, equals, hasCodeは必須なので、必ずオーバーライド！
  もしくはレコードクラスで作りましょう
:::



## テスト対象クラス
```java:EmployeeRepository.java
package com.example.persistence.repository;

import com.example.persistence.entity.Employee;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    /**
     * nameの部分一致(Containing)かつ(And)Emailの部分一致(Containing)で検索
     *
     * @param name  String
     * @param email String
     * @return List<Employee> 検索結果
     */
    List<Employee> findByNameContainingAndEmailContaining(String name, String email);
}

```
## テストクラス
```java:EmployeeRepositoryTest.java
package com.example.persistence.repository;

import com.example.persistence.entity.Employee;
import com.github.database.rider.core.api.configuration.DBUnit;
import com.github.database.rider.core.api.dataset.DataSet;
import com.github.database.rider.junit5.api.DBRider;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.beans.factory.annotation.Autowired;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

/**
 * @DataJpaTest - JPA関連のコンポーネントのみをロードし、インメモリデータベースを使ったリポジトリのテストを行うためのアノテーションです。
 * @AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE) - テスト時にデフォルトのインメモリデータベースを使わず、アプリケーションに設定されたデータベースをそのまま使用する設定です。
 * @DBUnit - Database Riderの設定を行うアノテーションです。ここでは、テーブル名の大文字小文字を区別しない、スキーマ名を指定する、接続情報を設定するなどのオプションを設定しています。
 * @Autowired - Springが依存性注入により、自動的にインスタンスを割り当てます。
 */
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@DBUnit(caseSensitiveTableNames = false, schema = "spring", url = "jdbc:mysql://localhost:3306/spring", user = "user", password = "P@ssw0rd")
class EmployeeRepositoryTest {

    @Autowired
    private EmployeeRepository employeeRepository;

　　/**
     * @DBRider - Database Riderを使用することを示します。
     * @DataSet(value = "initial_dataset1.yml") - テストメソッドの実行前に、指定されたYAML形式のデータセットをデータベースにロードします。
     */
    @DBRider
    @DataSet(value = "initial_dataset1.yml")
    void findByNameContainingAndEmailContaining() throws Exception{
        // 期待される結果のリストを定義
        List<Employee> expectedList = List.of(new Employee(109, "長谷川宏", LocalDate.parse("2024-04-01", DateTimeFormatter.ofPattern("yyyy-MM-dd")), "開発部", "hasegawa@example.com", LocalDate.parse("1990-11-29", DateTimeFormatter.ofPattern("yyyy-MM-dd"))));

        // 実際の結果のリストをリポジトリから取得
        List<Employee> actualList = employeeRepository.findByNameContainingAndEmailContaining("長谷川宏", "hasegawa@example.com");

        // 期待されるリストと実際のリストが等しいかを検証
        // assertIterableEqualsメソッドは、2つのIterable（ここではリスト）が要素ごとに等しいかどうかをチェックします。
        // 要素の順番も比較の対象となり、違いがあればテストが失敗します。
        // Employeeがequalsをオーバーライドしている必要があります。
        assertIterableEquals(expectedList, actualList);

    }

    // 他のテストメソッド
}
```

### @AutoConfigureTestDatabaseについて

`@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)` は、Spring Bootのテストで使用するデータベースを制御するためのアノテーションです。

- **@AutoConfigureTestDatabase**: Spring Bootのテスト用アノテーションで、テスト時にどのデータベースを使用するかを指定します。デフォルトでは、Spring Bootはテスト用にインメモリデータベース（例: H2, HSQLDBなど）を自動的に構成します。

- **replace = AutoConfigureTestDatabase.Replace.NONE**: このオプションを設定することで、Spring Bootはテスト時にインメモリデータベースを使わず、アプリケーションが設定ファイル（`application.properties` や `application.yml`）で指定したデータベース（通常はMySQL、PostgreSQLなどの外部データベース）を使用します。つまり、テスト時にも本番環境や開発環境と同じデータベース設定を使用するように指示しています。


### @DBRider について

@DBRider は、[DBUnit](http://dbunit.sourceforge.net/) のラッパーである DBRiderを使用して、テストデータを簡単に準備、管理するためのアノテーションです。Spring Bootアプリケーションのテストでデータベースに特定のデータセットをロードする際に使用されます。

- **@DBRider**: このアノテーションをテストクラスやテストメソッドに付与することで、DBRiderを利用したデータベースのセットアップが自動的に行われます。テスト実行時に指定されたデータセットがデータベースに挿入され、テストが開始されます。

#### @Test および@ExtendWithの継承

- **@Test の継承**: @DBRider アノテーションを付与することで、そのテストメソッドは暗黙的に @Test アノテーションの役割を果たします。つまり、@DBRider を付けたメソッドは、JUnitテストとして実行されます。このため、別途 @Test を付与する必要はありません。

- **@ExtendWith の継承**: @DBRider は内部的に @ExtendWith(DBUnitExtension.class) を使用しています。@ExtendWith を使うことで、テスト実行の前後にデータベースのセットアップやクリーンアップが適切に行われます。

#### 主な機能

- **データセットの指定**: @DataSet アノテーションと併用することで、テストで使用する初期データセット（例えばYAMLやJSON形式）を指定できます。このデータセットは、テストの前にデータベースにロードされます。

- **データベースのクリーンアップ**: テストの前後でデータベースの状態を管理します。通常、テストが開始される前にデータベースが指定されたデータセットで初期化され、テスト後にクリーンアップされるため、テストが他のテストに影響を与えることを防ぎます。



## 準備データセット
test/resources/ 配下に格納

```yml:initial_dataset1.yml
Employee:
  -
    id: "109"
    name: "長谷川宏"
    joined_date: "2024-04-01"
    department_name: "開発部"
    email: "hasegawa@example.com"
    birth_day: "1990-11-29"

```

# つまづいたところ
* 同一プロジェクト内に@SpringBootApplicationが付いたクラスが複数あるとエラーになった。
* @AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)の設定が必須だったこと。
* DTOクラス(今回はEmployee.java)にequalsがオーバーライドされていないと、`assertIterableEquals`が上手くいかない。一つ一つ値を確認しているからいいかと思ったけど内部では`equals`で確認している様子。
* 準備データセットの配置場所。手動で用意するとミスしがち。
* @SpringBootTestと併用しようとするとエラーが起きる(複数の`@BootstrapWith`が宣言されるため)。@DataJpaTestのみ使用します。



# 参考
https://virginity-system.com/2022/08/22/springboot-jpa%E3%81%AEentitymanager%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%9Fjunit%E3%83%86%E3%82%B9%E3%83%88%E3%81%AE%E8%A8%AD%E5%AE%9A/

https://qiita.com/opengl-8080/items/5ac56f7856d0214d1516

https://kazuhira-r.hatenablog.com/entry/2023/01/01/182411