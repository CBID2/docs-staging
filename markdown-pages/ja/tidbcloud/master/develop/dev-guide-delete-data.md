---
title: Delete Data
summary: Learn about the SQL syntax, best practices, and examples for deleting data.
---

# データの削除 {#delete-data}

このドキュメントでは、 [消去](/sql-statements/sql-statement-delete.md) SQL ステートメントを使用して TiDB 内のデータを削除する方法について説明します。

## 始める前に {#before-you-start}

このドキュメントを読む前に、次の準備が必要です。

-   [TiDB Cloud(サーバーレス層) で TiDBクラスタを構築する](/develop/dev-guide-build-cluster-in-cloud.md)
-   [スキーマ設計の概要](/develop/dev-guide-schema-design-overview.md) 、 [データベースを作成する](/develop/dev-guide-create-database.md) 、 [テーブルを作成する](/develop/dev-guide-create-table.md) 、および[セカンダリ インデックスの作成](/develop/dev-guide-create-secondary-indexes.md)を読み取る
-   [データの挿入](/develop/dev-guide-insert-data.md)

## SQL 構文 {#sql-syntax}

`DELETE`ステートメントは通常、次の形式です。


```sql
DELETE FROM {table} WHERE {filter}
```

|   パラメータ名   |       説明      |
| :--------: | :-----------: |
|  `{table}` |     テーブル名     |
| `{filter}` | フィルターのマッチング条件 |

この例は、 `DELETE`の単純な使用例のみを示しています。詳細については、 [DELETE 構文](/sql-statements/sql-statement-delete.md)を参照してください。

## ベストプラクティス {#best-practices}

データを削除するときに従うべきいくつかのベスト プラクティスを次に示します。

-   `DELETE`文には必ず`WHERE`節を指定してください。 `WHERE`句が指定されていない場合、TiDB はテーブル内の***すべて***の行を削除します。

<CustomContent platform="tidb">

-   多数の行 (たとえば、1 万行以上) を削除する場合は[一括削除](#bulk-delete)を使用します。これは、TiDB が 1 つのトランザクションのサイズを制限しているためです (デフォルトでは[txn-合計サイズ制限](/tidb-configuration-file.md#txn-total-size-limit) MB)。

</CustomContent>

<CustomContent platform="tidb-cloud">

-   TiDB はデフォルトで 1 つのトランザクションのサイズを 100 MB に制限しているため、多数の行 (たとえば、1 万行以上) を削除する場合は[一括削除](#bulk-delete)を使用します。

</CustomContent>

-   テーブル内のすべてのデータを削除する場合は、 `DELETE`ステートメントを使用しないでください。代わりに、 [`TRUNCATE`](/sql-statements/sql-statement-truncate.md)ステートメントを使用してください。
-   パフォーマンスに関する考慮事項については、 [パフォーマンスに関する考慮事項](#performance-considerations)を参照してください。

## 例 {#example}

特定の期間内にアプリケーション エラーが見つかり、この期間内の[評価](/develop/dev-guide-bookshop-schema-design.md#ratings-table)のすべてのデータ (たとえば、 `2022-04-15 00:00:00`から`2022-04-15 00:15:00`まで) を削除する必要があるとします。この場合、 `SELECT`ステートメントを使用して、削除するレコードの数を確認できます。


```sql
SELECT COUNT(*) FROM `ratings` WHERE `rated_at` >= "2022-04-15 00:00:00" AND `rated_at` <= "2022-04-15 00:15:00";
```

10,000 を超えるレコードが返された場合は、 [一括削除](#bulk-delete)を使用してそれらを削除します。

返されるレコードが 10,000 件未満の場合は、次の例を使用してそれらを削除します。

<SimpleTab groupId="language">
<div label="SQL" value="sql">

SQL では、例は次のとおりです。

```sql
DELETE FROM `ratings` WHERE `rated_at` >= "2022-04-15 00:00:00" AND `rated_at` <= "2022-04-15 00:15:00";
```

</div>

<div label="Java" value="java">

Java では、例は次のとおりです。

```java
// ds is an entity of com.mysql.cj.jdbc.MysqlDataSource

try (Connection connection = ds.getConnection()) {
    String sql = "DELETE FROM `bookshop`.`ratings` WHERE `rated_at` >= ? AND `rated_at` <= ?";
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    Calendar calendar = Calendar.getInstance();
    calendar.set(Calendar.MILLISECOND, 0);

    calendar.set(2022, Calendar.APRIL, 15, 0, 0, 0);
    preparedStatement.setTimestamp(1, new Timestamp(calendar.getTimeInMillis()));

    calendar.set(2022, Calendar.APRIL, 15, 0, 15, 0);
    preparedStatement.setTimestamp(2, new Timestamp(calendar.getTimeInMillis()));

    preparedStatement.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

</div>

<div label="Golang" value="golang">

Golang では、例は次のとおりです。

```go
package main

import (
    "database/sql"
    "fmt"
    "time"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, err := sql.Open("mysql", "root:@tcp(127.0.0.1:4000)/bookshop")
    if err != nil {
        panic(err)
    }
    defer db.Close()

    startTime := time.Date(2022, 04, 15, 0, 0, 0, 0, time.UTC)
    endTime := time.Date(2022, 04, 15, 0, 15, 0, 0, time.UTC)

    bulkUpdateSql := fmt.Sprintf("DELETE FROM `bookshop`.`ratings` WHERE `rated_at` >= ? AND `rated_at` <= ?")
    result, err := db.Exec(bulkUpdateSql, startTime, endTime)
    if err != nil {
        panic(err)
    }
    _, err = result.RowsAffected()
    if err != nil {
        panic(err)
    }
}
```

</div>

</SimpleTab>

<CustomContent platform="tidb">

`rated_at`フィールドは[日付と時刻の種類](/data-type-date-and-time.md)の`DATETIME`タイプです。タイムゾーンに関係なく、文字どおりの量として TiDB に格納されていると想定できます。一方、 `TIMESTAMP`タイプはタイムスタンプを格納するため、別の[タイムゾーン](/configure-time-zone.md)には別の時間文字列が表示されます。

</CustomContent>

<CustomContent platform="tidb-cloud">

`rated_at`フィールドは[日付と時刻の種類](/data-type-date-and-time.md)の`DATETIME`タイプです。タイムゾーンに関係なく、文字どおりの量として TiDB に格納されていると想定できます。一方、 `TIMESTAMP`タイプはタイムスタンプを格納するため、別のタイム ゾーンでは別の時刻文字列が表示されます。

</CustomContent>

> **ノート：**
>
> MySQL と同様に、 `TIMESTAMP`データ型は[2038年問題](https://en.wikipedia.org/wiki/Year_2038_problem)の影響を受けます。 2038 より大きい値を格納する場合は、 `DATETIME`型を使用することをお勧めします。

## パフォーマンスに関する考慮事項 {#performance-considerations}

### TiDB GC メカニズム {#tidb-gc-mechanism}

TiDB は、 `DELETE`ステートメントを実行した直後にデータを削除しません。代わりに、データを削除の準備ができているとマークします。次に、TiDB GC (ガベージ コレクション) が古いデータをクリーンアップするのを待ちます。したがって、 `DELETE`ステートメントは、ディスク使用量をすぐには削減し***ません***。

デフォルトでは、GC は 10 分ごとに 1 回トリガーされます。各 GC は、 **safe_point**と呼ばれる時点を計算します。この時点より前のデータは再使用されないため、TiDB は安全にクリーンアップできます。

詳細については、 [GC メカニズム](/garbage-collection-overview.md)を参照してください。

### 統計情報の更新 {#update-statistical-information}

TiDB は[統計情報](/statistics.md)を使用してインデックスの選択を決定します。大量のデータを削除すると、インデックスが正しく選択されない可能性が高くなります。 [手動収集](/statistics.md#manual-collection)を使用して統計を更新できます。これは、TiDB オプティマイザーに SQL パフォーマンス最適化のためのより正確な統計情報を提供します。

## 一括削除 {#bulk-delete}

テーブルから複数行のデータを削除する必要がある場合は、 [`DELETE`例](#example)句を選択し、 `WHERE`句を使用して、削除する必要があるデータをフィルタリングできます。

<CustomContent platform="tidb">

ただし、多数の行 (1 万行以上) を削除する必要がある場合は、データを繰り返し削除することをお勧めします。つまり、削除が完了するまで繰り返しごとにデータの一部を削除します。これは、TiDB が[`txn-total-size-limit`](/tidb-configuration-file.md#txn-total-size-limit)つのトランザクションのサイズを制限しているためです (デフォルトでは 1、100 MB)。プログラムまたはスクリプトでループを使用して、このような操作を実行できます。

</CustomContent>

<CustomContent platform="tidb-cloud">

ただし、多数の行 (1 万行以上) を削除する必要がある場合は、データを繰り返し削除することをお勧めします。つまり、削除が完了するまで繰り返しごとにデータの一部を削除します。これは、TiDB がデフォルトで 1 つのトランザクションのサイズを 100 MB に制限しているためです。プログラムまたはスクリプトでループを使用して、このような操作を実行できます。

</CustomContent>

このセクションでは、一括削除を完了するために`SELECT`と`DELETE`の組み合わせを実行する方法を示す、反復的な削除操作を処理するスクリプトを作成する例を示します。

### 一括削除ループを作成する {#write-a-bulk-delete-loop}

アプリケーションまたはスクリプトのループに`DELETE`ステートメントを記述し、 `WHERE`句を使用してデータをフィルター処理し、 `LIMIT`句を使用して 1 つのステートメントで削除する行数を制限できます。

### 一括削除の例 {#bulk-delete-example}

特定の期間内にアプリケーション エラーが見つかったとします。この期間内に[評価](/develop/dev-guide-bookshop-schema-design.md#ratings-table)のすべてのデータ (たとえば`2022-04-15 00:00:00`から`2022-04-15 00:15:00`まで) を削除する必要があり、15 分間で 10,000 を超えるレコードが書き込まれます。次のように実行できます。

<SimpleTab groupId="language">
<div label="Java" value="java">

Java での一括削除の例は次のとおりです。


```java
package com.pingcap.bulkDelete;

import com.mysql.cj.jdbc.MysqlDataSource;

import java.sql.*;
import java.util.*;
import java.util.concurrent.TimeUnit;

public class BatchDeleteExample
{
    public static void main(String[] args) throws InterruptedException {
        // Configure the example database connection.

        // Create a mysql data source instance.
        MysqlDataSource mysqlDataSource = new MysqlDataSource();

        // Set server name, port, database name, username and password.
        mysqlDataSource.setServerName("localhost");
        mysqlDataSource.setPortNumber(4000);
        mysqlDataSource.setDatabaseName("bookshop");
        mysqlDataSource.setUser("root");
        mysqlDataSource.setPassword("");

        while (true) {
            batchDelete(mysqlDataSource);
            TimeUnit.SECONDS.sleep(1);
        }
    }

    public static void batchDelete (MysqlDataSource ds) {
        try (Connection connection = ds.getConnection()) {
            String sql = "DELETE FROM `bookshop`.`ratings` WHERE `rated_at` >= ? AND `rated_at` <= ? LIMIT 1000";
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            Calendar calendar = Calendar.getInstance();
            calendar.set(Calendar.MILLISECOND, 0);

            calendar.set(2022, Calendar.APRIL, 15, 0, 0, 0);
            preparedStatement.setTimestamp(1, new Timestamp(calendar.getTimeInMillis()));

            calendar.set(2022, Calendar.APRIL, 15, 0, 15, 0);
            preparedStatement.setTimestamp(2, new Timestamp(calendar.getTimeInMillis()));

            int count = preparedStatement.executeUpdate();
            System.out.println("delete " + count + " data");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

各反復で、 `DELETE`は`2022-04-15 00:00:00`から`2022-04-15 00:15:00`までの最大 1000 行を削除します。

</div>

<div label="Golang" value="golang">

Golang での一括削除の例は次のとおりです。


```go
package main

import (
    "database/sql"
    "fmt"
    "time"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, err := sql.Open("mysql", "root:@tcp(127.0.0.1:4000)/bookshop")
    if err != nil {
        panic(err)
    }
    defer db.Close()

    affectedRows := int64(-1)
    startTime := time.Date(2022, 04, 15, 0, 0, 0, 0, time.UTC)
    endTime := time.Date(2022, 04, 15, 0, 15, 0, 0, time.UTC)

    for affectedRows != 0 {
        affectedRows, err = deleteBatch(db, startTime, endTime)
        if err != nil {
            panic(err)
        }
    }
}

// deleteBatch delete at most 1000 lines per batch
func deleteBatch(db *sql.DB, startTime, endTime time.Time) (int64, error) {
    bulkUpdateSql := fmt.Sprintf("DELETE FROM `bookshop`.`ratings` WHERE `rated_at` >= ? AND `rated_at` <= ? LIMIT 1000")
    result, err := db.Exec(bulkUpdateSql, startTime, endTime)
    if err != nil {
        return -1, err
    }
    affectedRows, err := result.RowsAffected()
    if err != nil {
        return -1, err
    }

    fmt.Printf("delete %d data\n", affectedRows)
    return affectedRows, nil
}
```

各反復で、 `DELETE`は`2022-04-15 00:00:00`から`2022-04-15 00:15:00`までの最大 1000 行を削除します。

</div>

</SimpleTab>
