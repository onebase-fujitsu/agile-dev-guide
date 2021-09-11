---
title: "3日目"
weight: 3
bookToc: true
---

# 3日目

2日目まででタスク一覧をサーバに対して要求して、レスポンスに応じてタスク一覧を画面に描画するフロントエンドができました。
ただ、現時点でリクエストに対して応答するバックエンドがまだありませんので、これを作っていきましょう。

## バックエンドの初期設定

### Spring Initializr
Spring Bootのプロジェクトを作成するときは [Spring Initializr](https://start.spring.io/) を使います。

![Spring Initializr](SpringInitializr.jpg)

今回はGradle Projectで開発言語はKotlinにします。

Spring Bootのバージョンはその時の安定版を指定すると良いです。

Artifact名は今回はtodo-app-serverとしました。
依存ライブラリですが、後からでも追加できますので、ここではいったん、Spring Webのみ追加しました。

これでGenerateしましょう。雛形となるプロジェクトがダウンロードされるはずです。

### DBの設定

例によってアプリ作成は初期設定が一番大変です。頑張っていきましょう。
まず、DBの設定をしていきましょう。

今回DBは [PostgreSQL](https://www.postgresql.org/) を使用しようと思います。
ソフトウェアテストの章でつかったサンプルアプリケーションではH2をインメモリで動かしていたのですが、
流石に実際の開発のときにそんなことはしないので、実際の開発に寄せる意味でもPostgreSQLにしてみます。

まだPostgreSQLをインストールしていない方はPostgreSQLを導入してください。

PostgreSQLをサービスとしてローカルで動いているのを確認したら、
[createdb](https://www.postgresql.jp/document/9.4/html/app-createdb.html) コマンドを実行してtodo_dbを作っておきましょう。

```shell
createdb todo_db
```

まず、DB接続に必要なライブラリを追加しましょう。build.gradle.ktsを開いたらdependenciesを以下のように変更してください。

```kotlin
// build.gradle.kts
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
    implementation("org.jetbrains.kotlin:kotlin-reflect")
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
    implementation("org.springframework.boot:spring-boot-starter-jdbc")   // 追記
    runtimeOnly("org.postgresql:postgresql")                              // 追記
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

次にsrc/resources/application.propertiesがありますが、yamlで記述したほうが見通しが良くなるので、
このapplication.propertiesをapplication.ymlに変更します。

```yaml
// application.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/${DB_NAME:todo_db}
    username: onebase
    password:
```

設定ファイルはこのように変更します。**usernameは`createdb`コマンドを実行した環境に依存しますので、皆様の環境に合わせて書き換えてください。**
この状態でアプリケーションを起動してみましょう。

```shell
./gradlew bootRun
```

```
onebase@Onebase-Maguro todo-app-server % ./gradlew bootRun

> Task :bootRun

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.5.4)

2021-09-12 04:38:21.169  INFO 74012 --- [           main] c.f.t.TodoAppServerApplicationKt         : Starting TodoAppServerApplicationKt using Java 13.0.2 on Onebase-Maguro.local with PID 74012 (/Users/onebase/IdeaProjects/todo-app-server/build/classes/kotlin/main started by onebase in /Users/onebase/IdeaProjects/todo-app-server)
2021-09-12 04:38:21.171  INFO 74012 --- [           main] c.f.t.TodoAppServerApplicationKt         : No active profile set, falling back to default profiles: default
2021-09-12 04:38:22.054  INFO 74012 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-09-12 04:38:22.063  INFO 74012 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-09-12 04:38:22.064  INFO 74012 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.52]
2021-09-12 04:38:22.122  INFO 74012 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-09-12 04:38:22.122  INFO 74012 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 910 ms
2021-09-12 04:38:22.451  INFO 74012 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-09-12 04:38:22.459  INFO 74012 --- [           main] c.f.t.TodoAppServerApplicationKt         : Started TodoAppServerApplicationKt in 1.844 seconds
<==========---> 83% EXECUTING [16s]
> :bootRun

```

ひとまずこのような表示が出たらServerとPostgreSQL DBの接続はうまくいっていますので、`Control+C`を押してサーバを止めておきましょう。

### DBのマイグレーションツールの導入

DBのマイグレーションツールを導入してみましょう。
先程`createdb`コマンドを実行してDBを作りましたが、まだテーブルを全く作ってません。
DBのスキーマを手動で管理するのはメチャクチャ大変ですので、マイグレーションツールを導入するとスキーマの作成や、スキーマ変更に伴うデータのマイグレーションを自動化することができます。

ここでは [Flyway](https://flywaydb.org/) を使います。

再び、build.gradle.ktsを開いたら以下の依存を追加してください。
```kotlin
// build.gradle.kts
dependencies {
	implementation("org.springframework.boot:spring-boot-starter-jdbc")
	implementation("org.springframework.boot:spring-boot-starter-web")
	implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
	implementation("org.jetbrains.kotlin:kotlin-reflect")
	implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
	implementation("org.flywaydb:flyway-core")                       // 追記
	runtimeOnly("org.postgresql:postgresql")
	testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

次に、src/main/resources/application.ymlを再び開いてFlywayの設定を記入しましょう。

```yaml
// application.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/${DB_NAME:todo_db}
    username: onebase
    password:
  flyway:
    baseline-on-migrate: true
    url: jdbc:postgresql://localhost:5432/${DB_NAME:todo_db}
    user: onebase
    password:
```

これでFlywayを使う準備が整いました。では実際にマイグレーションをかけてテーブルを作ってみましょう。
src/main/resources配下にdbというディレクトリを作成し、さらにその配下にmigrationというディレクトリを作成します。
そして、そのmigration配下に`VyyyyMMddHHmmss__CreateTodoTable.sql`というファイルを新規に作ってください。
'yyyyMMddHHmmss'の部分は実際に作成作業をした時間に置き換えてください。
ファイル名がこの命名規約に従っていることがとても大事です。

```
src
├── main
│     ├── kotlin
│     │     └── com
│     │         └── fujitsu
│     │             └── todoappserver
│     │                 └── TodoAppServerApplication.kt
│     └── resources
│         ├── application.yml
│         ├── db
│         │     └── migration
│         │         └── V20210912045400__CreateTodoTable.sql
│         ├── static
│         └── templates
└── test
    └── kotlin
        └── com
            └── fujitsu
                └── todoappserver
                    └── TodoAppServerApplicationTests.kt

```

筆者の例では「V20210912045400__CreateTodoTable.sql」というファイル名にしています。（このドキュメントは朝4時に書いています。）

このファイルにはマイグレーション用のSQLを記述します。

```sql
create table todo (
    id serial primary key,
    title varchar not null,
    completed boolean not null default false
);
```

最初のSQLはこのようにしました。
この状態で再度アプリケーションを起動してみましょう。

```shell
./gradlew bootRun
```

正常に起動したら、どのようなツールでも構いませんのでtodo_dbの状態をみてみましょう。

![flyway migration](flyway_migration.jpg)

flyway_schema_historyというテーブル共にtodoテーブルがFlywayによって作られていたら成功です。
Flywayはアプリケーション起動時にdb/migrationディレクトリ配下のSQLを確認して、DBのマイグレーションを実行してくれます。

これでバックエンド開発の下準備が整いました。ここまでのソースコードは
[https://github.com/Onebase-Fujitsu/todo-app-server/tree/step1](https://github.com/Onebase-Fujitsu/todo-app-server/tree/step1)
に置いてあります。

