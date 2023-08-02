---
title: Third-Party Tools Supported by TiDB
summary: Learn about third-party tools supported by TiDB.
---

# TiDB がサポートするサードパーティ ツール {#third-party-tools-supported-by-tidb}

> **ノート：**
>
> このドキュメントでは、TiDB でサポートされる一般的な[サードパーティツール](https://en.wikipedia.org/wiki/Third-party_source)のみをリストします。他のサードパーティ ツールの一部はリストされていません。これは、それらがサポートされていないためではなく、TiDB と互換性のない機能を使用しているかどうか PingCAP が不明であるためです。

TiDB は[MySQLプロトコルとの高い互換性](/mysql-compatibility.md)なので、ほとんどの MySQL ドライバー、ORM フレームワーク、および MySQL に適応するその他のツールは TiDB と互換性があります。このドキュメントでは、これらのツールと TiDB のサポート レベルに焦点を当てます。

## サポートレベル {#support-level}

PingCAP はコミュニティと連携し、サードパーティ ツールに次のサポート レベルを提供します。

-   ***Full*** : TiDB が対応するサードパーティ ツールのほとんどの機能とすでに互換性があり、その新しいバージョンとの互換性が維持されていることを示します。 PingCAP は、ツールの最新バージョンとの互換性テストを定期的に実施します。
-   ***互換性***: 対応するサードパーティ ツールが MySQL に適合しており、TiDB が MySQL プロトコルと高い互換性があるため、TiDB はツールのほとんどの機能を使用できることを示します。ただし、PingCAP はツールのすべての機能について完全なテストを完了していないため、予期しない動作が発生する可能性があります。

> **ノート：**
>
> 特に指定しない限り、**Driver**または**ORM フレームワーク**には[アプリケーションの再試行とエラー処理](/develop/dev-guide-transaction-troubleshoot.md#application-retry-and-error-handling)のサポートは含まれません。

このドキュメントに記載されているツールを使用して TiDB に接続するときに問題が発生した場合は、このツールのサポートを促進するための詳細を記載して GitHub に[問題](https://github.com/pingcap/tidb/issues/new?assignees=&#x26;labels=type%2Fquestion&#x26;template=general-question.md)を送信してください。

## Driver {#driver}

<CustomContent platform="tidb">

<table><thead><tr><th>言語</th><th>Driver</th><th>最新のテスト済みバージョン</th><th>サポートレベル</th><th>TiDBアダプター</th><th>チュートリアル</th></tr></thead><tbody><tr><td>C</td><td> <a href="https://dev.mysql.com/doc/c-api/8.0/en/c-api-introduction.html" target="_blank" referrerpolicy="no-referrer-when-downgrade">libmysqlクライアント</a></td><td>8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>C#(.ネット)</td><td> <a href="https://downloads.mysql.com/archives/c-net/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQL コネクタ/NET</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>ODBC</td><td> <a href="https://downloads.mysql.com/archives/c-odbc/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQLコネクタ/ODBC</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>行く</td><td><a href="https://github.com/go-sql-driver/mysql" target="_blank" referrerpolicy="no-referrer-when-downgrade">Go-MySQL-ドライバー</a></td><td>v1.6.0</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-golang-sql-driver">TiDB と Go-MySQL-Driver を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td>Java</td><td><a href="https://dev.mysql.com/downloads/connector/j/" target="_blank" referrerpolicy="no-referrer-when-downgrade">JDBC</a></td><td> 8.0</td><td>満杯</td><td><ul><li><a href="/tidb/v7.1/dev-guide-choose-driver-or-orm#java-drivers" data-href="/tidb/v7.1/dev-guide-choose-driver-or-orm#java-drivers">pingcap/mysql-connector-j</a></li><li> <a href="/tidb/v7.1/dev-guide-choose-driver-or-orm#tidb-loadbalance" data-href="/tidb/v7.1/dev-guide-choose-driver-or-orm#tidb-loadbalance">pingcap/tidb-loadbalance</a></li></ul></td><td> <a href="/tidb/v7.1/dev-guide-sample-application-java-jdbc">TiDB と JDBC を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td>JavaScript</td><td> <a href="https://github.com/mysqljs/mysql" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysql</a></td><td> v2.18.1</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>PHP</td><td> <a href="https://dev.mysql.com/downloads/connector/php-mysqlnd/" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysqlnd</a></td><td> PHP 5.4+</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="3">パイソン</td><td><a href="https://dev.mysql.com/doc/connector-python/en/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQL コネクタ/Python</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-python-mysql-connector">TiDB と MySQL Connector/Python を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://mysqlclient.readthedocs.io/" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysqlクライアント</a></td><td>2.1.1</td><td>互換性</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-python-mysqlclient">TiDB と mysqlclient を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://pypi.org/project/PyMySQL/" target="_blank" referrerpolicy="no-referrer-when-downgrade">PyMySQL</a></td><td> 1.0.2</td><td>互換性</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-python-pymysql">TiDB と PyMySQL を使用してシンプルな CRUD アプリを構築する</a></td></tr></tbody></table>

</CustomContent>

<CustomContent platform="tidb-cloud">

<table><thead><tr><th>言語</th><th>Driver</th><th>最新のテスト済みバージョン</th><th>サポートレベル</th><th>TiDBアダプター</th><th>チュートリアル</th></tr></thead><tbody><tr><td>C</td><td> <a href="https://dev.mysql.com/doc/c-api/8.0/en/c-api-introduction.html" target="_blank" referrerpolicy="no-referrer-when-downgrade">libmysqlクライアント</a></td><td>8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>C#(.ネット)</td><td> <a href="https://downloads.mysql.com/archives/c-net/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQL コネクタ/NET</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>ODBC</td><td> <a href="https://downloads.mysql.com/archives/c-odbc/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQLコネクタ/ODBC</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>行く</td><td><a href="https://github.com/go-sql-driver/mysql" target="_blank" referrerpolicy="no-referrer-when-downgrade">Go-MySQL-ドライバー</a></td><td>v1.6.0</td><td>満杯</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-golang-sql-driver">TiDB と Go-MySQL-Driver を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td>Java</td><td><a href="https://dev.mysql.com/downloads/connector/j/" target="_blank" referrerpolicy="no-referrer-when-downgrade">JDBC</a></td><td> 8.0</td><td>満杯</td><td><ul><li><a href="/tidbcloud/dev-guide-choose-driver-or-orm#java-drivers" data-href="/tidbcloud/dev-guide-choose-driver-or-orm#java-drivers">pingcap/mysql-connector-j</a></li><li> <a href="/tidbcloud/dev-guide-choose-driver-or-orm#tidb-loadbalance" data-href="/tidbcloud/dev-guide-choose-driver-or-orm#tidb-loadbalance">pingcap/tidb-loadbalance</a></li></ul></td><td> <a href="/tidbcloud/dev-guide-sample-application-java-jdbc">TiDB と JDBC を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td>JavaScript</td><td> <a href="https://github.com/mysqljs/mysql" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysql</a></td><td> v2.18.1</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>PHP</td><td> <a href="https://dev.mysql.com/downloads/connector/php-mysqlnd/" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysqlnd</a></td><td> PHP 5.4+</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="3">パイソン</td><td><a href="https://dev.mysql.com/doc/connector-python/en/" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQL コネクタ/Python</a></td><td> 8.0</td><td>互換性</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-python-mysql-connector">TiDB と MySQL Connector/Python を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://mysqlclient.readthedocs.io/" target="_blank" referrerpolicy="no-referrer-when-downgrade">mysqlクライアント</a></td><td>2.1.1</td><td>互換性</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-python-mysqlclient">TiDB と mysqlclient を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://pypi.org/project/PyMySQL/" target="_blank" referrerpolicy="no-referrer-when-downgrade">PyMySQL</a></td><td> 1.0.2</td><td>互換性</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-python-pymysql">TiDB と PyMySQL を使用してシンプルな CRUD アプリを構築する</a></td></tr></tbody></table>

</CustomContent>

## ORM {#orm}

<CustomContent platform="tidb">

<table><thead><tr><th>言語</th><th>ORMフレームワーク</th><th>最新のテスト済みバージョン</th><th>サポートレベル</th><th>TiDBアダプター</th><th>チュートリアル</th></tr></thead><tbody><tr><td rowspan="5">行く</td><td><a href="https://github.com/go-gorm/gorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">ゴーム</a></td><td>v1.23.5</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-golang-gorm">TiDB と GORM を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://github.com/beego/beego" target="_blank" referrerpolicy="no-referrer-when-downgrade">ビーゴ</a></td><td>v2.0.3</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td> <a href="https://github.com/upper/db" target="_blank" referrerpolicy="no-referrer-when-downgrade">アッパー/データベース</a></td><td>v4.5.2</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://gitea.com/xorm/xorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">xorm</a></td><td> v1.3.1</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://github.com/ent/ent" target="_blank" referrerpolicy="no-referrer-when-downgrade">エント</a></td><td>v0.11.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="4">Java</td><td><a href="https://hibernate.org/orm/" target="_blank" referrerpolicy="no-referrer-when-downgrade">休止状態</a></td><td>6.1.0.最終回</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-java-hibernate">TiDB と Hibernate を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://mybatis.org/mybatis-3/" target="_blank" referrerpolicy="no-referrer-when-downgrade">マイバティス</a></td><td>v3.5.10</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-java-mybatis">TiDB と Mybatis を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://spring.io/projects/spring-data-jpa/" target="_blank" referrerpolicy="no-referrer-when-downgrade">Spring Data JPA</a></td><td> 2.7.2</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-java-spring-boot">TiDB と Spring Boot を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td> <a href="https://github.com/jOOQ/jOOQ" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジョーク</a></td><td>v3.16.7 (オープンソース)</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td>ルビー</td><td><a href="https://guides.rubyonrails.org/active_record_basics.html" target="_blank" referrerpolicy="no-referrer-when-downgrade">アクティブなレコード</a></td><td>v7.0</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="4">JavaScript / TypeScript</td><td> <a href="https://www.npmjs.com/package/sequelize" target="_blank" referrerpolicy="no-referrer-when-downgrade">続編化する</a></td><td>v6.20.1</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://knexjs.org/" target="_blank" referrerpolicy="no-referrer-when-downgrade">Knex.js</a></td><td> v1.0.7</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://www.prisma.io/" target="_blank" referrerpolicy="no-referrer-when-downgrade">プリズマクライアント</a></td><td>3.15.1</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://www.npmjs.com/package/typeorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">TypeORM</a></td><td> v0.3.6</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>PHP</td><td><a href="https://laravel.com/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ララベル</a></td><td>v9.1.10</td><td>互換性</td><td><a href="https://github.com/colopl/laravel-tidb" target="_blank" referrerpolicy="no-referrer-when-downgrade">laravel-tidb</a></td><td>該当なし</td></tr><tr><td rowspan="4">パイソン</td><td><a href="https://pypi.org/project/Django/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジャンゴ</a></td><td>v4.1</td><td>満杯</td><td><a href="https://github.com/pingcap/django-tidb" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジャンゴティブ</a></td><td>該当なし</td></tr><tr><td><a href="https://www.sqlalchemy.org/" target="_blank" referrerpolicy="no-referrer-when-downgrade">SQLアルケミー</a></td><td>v1.4.37</td><td>満杯</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-python-sqlalchemy">TiDB と SQLAlchemy を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://github.com/coleifer/peewee/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ピーピー</a></td><td>v3.14.10</td><td>互換性</td><td>該当なし</td><td><a href="/tidb/v7.1/dev-guide-sample-application-python-peewee">TiDB と peewee を使用してシンプルな CRUD アプリを構築する</a></td></tr></tbody></table>

</CustomContent>

<CustomContent platform="tidb-cloud">

<table><thead><tr><th>言語</th><th>ORMフレームワーク</th><th>最新のテスト済みバージョン</th><th>サポートレベル</th><th>TiDBアダプター</th><th>チュートリアル</th></tr></thead><tbody><tr><td rowspan="5">行く</td><td><a href="https://github.com/go-gorm/gorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">ゴーム</a></td><td>v1.23.5</td><td>満杯</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-golang-gorm">TiDB と GORM を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://github.com/beego/beego" target="_blank" referrerpolicy="no-referrer-when-downgrade">ビーゴ</a></td><td>v2.0.3</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td> <a href="https://github.com/upper/db" target="_blank" referrerpolicy="no-referrer-when-downgrade">アッパー/データベース</a></td><td>v4.5.2</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://gitea.com/xorm/xorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">xorm</a></td><td> v1.3.1</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://github.com/ent/ent" target="_blank" referrerpolicy="no-referrer-when-downgrade">エント</a></td><td>v0.11.0</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="4">Java</td><td><a href="https://hibernate.org/orm/" target="_blank" referrerpolicy="no-referrer-when-downgrade">休止状態</a></td><td>6.1.0.最終回</td><td>満杯</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-java-hibernate">TiDB と Hibernate を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://mybatis.org/mybatis-3/" target="_blank" referrerpolicy="no-referrer-when-downgrade">マイバティス</a></td><td>v3.5.10</td><td>満杯</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-java-mybatis">TiDB と Mybatis を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://spring.io/projects/spring-data-jpa/" target="_blank" referrerpolicy="no-referrer-when-downgrade">Spring Data JPA</a></td><td> 2.7.2</td><td>満杯</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-java-spring-boot">TiDB と Spring Boot を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td> <a href="https://github.com/jOOQ/jOOQ" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジョーク</a></td><td>v3.16.7 (オープンソース)</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td>ルビー</td><td><a href="https://guides.rubyonrails.org/active_record_basics.html" target="_blank" referrerpolicy="no-referrer-when-downgrade">アクティブなレコード</a></td><td>v7.0</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td rowspan="4">JavaScript / TypeScript</td><td> <a href="https://www.npmjs.com/package/sequelize" target="_blank" referrerpolicy="no-referrer-when-downgrade">続編化する</a></td><td>v6.20.1</td><td>満杯</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://knexjs.org/" target="_blank" referrerpolicy="no-referrer-when-downgrade">Knex.js</a></td><td> v1.0.7</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://www.prisma.io/" target="_blank" referrerpolicy="no-referrer-when-downgrade">プリズマクライアント</a></td><td>3.15.1</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td><a href="https://www.npmjs.com/package/typeorm" target="_blank" referrerpolicy="no-referrer-when-downgrade">TypeORM</a></td><td> v0.3.6</td><td>互換性</td><td>該当なし</td><td>該当なし</td></tr><tr><td>PHP</td><td><a href="https://laravel.com/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ララベル</a></td><td>v9.1.10</td><td>互換性</td><td><a href="https://github.com/colopl/laravel-tidb" target="_blank" referrerpolicy="no-referrer-when-downgrade">laravel-tidb</a></td><td>該当なし</td></tr><tr><td rowspan="4">パイソン</td><td><a href="https://pypi.org/project/Django/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジャンゴ</a></td><td>v4.1</td><td>満杯</td><td><a href="https://github.com/pingcap/django-tidb" target="_blank" referrerpolicy="no-referrer-when-downgrade">ジャンゴティブ</a></td><td>該当なし</td></tr><tr><td><a href="https://github.com/coleifer/peewee/" target="_blank" referrerpolicy="no-referrer-when-downgrade">ピーピー</a></td><td>v3.14.10</td><td>互換性</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-python-peewee">TiDB と peewee を使用してシンプルな CRUD アプリを構築する</a></td></tr><tr><td><a href="https://www.sqlalchemy.org/" target="_blank" referrerpolicy="no-referrer-when-downgrade">SQLアルケミー</a></td><td>v1.4.37</td><td>互換性</td><td>該当なし</td><td><a href="/tidbcloud/dev-guide-sample-application-python-sqlalchemy">TiDB と SQLAlchemy を使用してシンプルな CRUD アプリを構築する</a></td></tr></tbody></table>

</CustomContent>

## GUI {#gui}

| GUI                                                       | 最新のテスト済みバージョン | サポートレベル | チュートリアル |
| --------------------------------------------------------- | ------------- | ------- | ------- |
| [Dビーバー](https://dbeaver.io/)                              | 22.1.0        | 互換性     | 該当なし    |
| [MySQL 用 Navicat](https://www.navicat.com/)               | 16.0.14       | 互換性     | 該当なし    |
| [MySQL ワークベンチ](https://www.mysql.com/products/workbench/) | 8.0           | 互換性     | 該当なし    |

<table><thead><tr><th>IDE</th><th>プラグイン</th><th>サポートレベル</th><th>チュートリアル</th></tr></thead><tbody><tr><td><a href="https://www.jetbrains.com/datagrip/" target="_blank" referrerpolicy="no-referrer-when-downgrade">データグリップ</a></td><td>該当なし</td><td>互換性</td><td>該当なし</td></tr><tr><td><a href="https://www.jetbrains.com/idea/" target="_blank" referrerpolicy="no-referrer-when-downgrade">インテリJアイデア</a></td><td>該当なし</td><td>互換性</td><td>該当なし</td></tr><tr><td rowspan="2"><a href="https://code.visualstudio.com/" target="_blank" referrerpolicy="no-referrer-when-downgrade">Visual Studioコード</a></td><td><a href="https://marketplace.visualstudio.com/items?itemName=dragonly.ticode" target="_blank" referrerpolicy="no-referrer-when-downgrade">潮</a></td><td>互換性</td><td>該当なし</td></tr><tr><td><a href="https://marketplace.visualstudio.com/items?itemName=formulahendry.vscode-mysql" target="_blank" referrerpolicy="no-referrer-when-downgrade">MySQL</a></td><td>互換性</td><td>該当なし</td></tr></tbody></table>