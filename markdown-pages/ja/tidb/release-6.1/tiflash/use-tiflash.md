---
title: Use TiFlash
---

データのインポートから TPC-H データセットでのクエリまでのプロセス全体を体験するには、 [TiDB HTAPのクイック スタート ガイド](/quick-start-with-htap.md)を参照してください。

# TiFlash を使用する {#use-tiflash}

TiFlash が展開された後、データの複製は自動的に開始されません。レプリケートするテーブルを手動で指定する必要があります。

TiDB を使用して中規模の分析処理用の TiFlash レプリカを読み取るか、TiSpark を使用して大規模な分析処理用の TiFlash レプリカを読み取ることができます。これは、独自のニーズに基づいています。詳細については、次のセクションを参照してください。

-   [TiDB を使用して TiFlash レプリカを読み取る](#use-tidb-to-read-tiflash-replicas)
-   [TiSpark を使用して TiFlash レプリカを読み取る](#use-tispark-to-read-tiflash-replicas)

## TiFlash レプリカの作成 {#create-tiflash-replicas}

このセクションでは、テーブルとデータベースの TiFlash レプリカを作成し、レプリカのスケジュールに使用できるゾーンを設定する方法について説明します。

### テーブルの TiFlash レプリカを作成する {#create-tiflash-replicas-for-tables}

TiFlash が TiKV クラスターに接続された後、デフォルトではデータ複製は開始されません。 MySQL クライアント経由で DDL ステートメントを TiDB に送信して、特定のテーブルの TiFlash レプリカを作成できます。


```sql
ALTER TABLE table_name SET TIFLASH REPLICA count;
```

上記のコマンドのパラメーターは、次のように記述されます。

-   `count`はレプリカの数を示します。値が`0`の場合、レプリカは削除されます。

同じテーブルで複数の DDL ステートメントを実行する場合、最後のステートメントのみが確実に有効になります。次の例では、テーブル`tpch50`に対して 2 つの DDL ステートメントが実行されますが、2 番目のステートメント (レプリカを削除するため) のみが有効になります。

テーブルのレプリカを 2 つ作成します。


```sql
ALTER TABLE `tpch50`.`lineitem` SET TIFLASH REPLICA 2;
```

レプリカを削除します。


```sql
ALTER TABLE `tpch50`.`lineitem` SET TIFLASH REPLICA 0;
```

**ノート：**

-   テーブル`t`が上記の DDL ステートメントによって TiFlash に複製される場合、次のステートメントを使用して作成されたテーブルも自動的に TiFlash に複製されます。

    
    ```sql
    CREATE TABLE table_name like t;
    ```

-   v4.0.6 より前のバージョンでは、 TiDB Lightningを使用してデータをインポートする前に TiFlash レプリカを作成すると、データのインポートは失敗します。テーブルの TiFlash レプリカを作成する前に、テーブルにデータをインポートする必要があります。

-   TiDB とTiDB Lightningが両方とも v4.0.6 以降の場合、テーブルに TiFlash レプリカがあるかどうかに関係なく、 TiDB Lightningを使用してそのテーブルにデータをインポートできます。これにより、 TiDB Lightning手順が遅くなる可能性があることに注意してください。これは、Lightning ホストの NIC 帯域幅、TiFlash ノードの CPU とディスクの負荷、および TiFlash レプリカの数に依存します。

-   PD スケジューリングのパフォーマンスが低下するため、1,000 を超えるテーブルを複製しないことをお勧めします。この制限は、以降のバージョンで削除されます。

-   v5.1 以降のバージョンでは、システム テーブルのレプリカの設定はサポートされなくなりました。クラスタをアップグレードする前に、関連するシステム テーブルのレプリカをクリアする必要があります。そうしないと、クラスターを新しいバージョンにアップグレードした後で、システム テーブルのレプリカ設定を変更できません。

#### レプリケーションの進行状況を確認する {#check-replication-progress}

次のステートメントを使用して、特定のテーブルの TiFlash レプリカのステータスを確認できます。テーブルは`WHERE`句を使用して指定されます。 `WHERE`句を削除すると、すべてのテーブルのレプリカ ステータスがチェックされます。


```sql
SELECT * FROM information_schema.tiflash_replica WHERE TABLE_SCHEMA = '<db_name>' and TABLE_NAME = '<table_name>';
```

上記のステートメントの結果:

-   `AVAILABLE`は、このテーブルの TiFlash レプリカが使用可能かどうかを示します。 `1`は利用可能であることを意味し、 `0`は利用できないことを意味します。レプリカが利用可能になると、このステータスは変わりません。 DDL ステートメントを使用してレプリカの数を変更すると、レプリケーション ステータスが再計算されます。
-   `PROGRESS`は、レプリケーションの進行状況を意味します。値は`0.0` ～ `1.0`です。 `1`は、少なくとも 1 つのレプリカが複製されていることを意味します。

### データベースの TiFlash レプリカを作成する {#create-tiflash-replicas-for-databases}

テーブルの TiFlash レプリカを作成するのと同様に、MySQL クライアントを介して TiDB に DDL ステートメントを送信し、特定のデータベース内のすべてのテーブルの TiFlash レプリカを作成できます。


```sql
ALTER DATABASE db_name SET TIFLASH REPLICA count;
```

このステートメントでは、 `count`はレプリカの数を示します。 `0`に設定すると、レプリカが削除されます。

例:

-   データベース`tpch50`内のすべてのテーブルに対して 2 つのレプリカを作成します。

    
    ```sql
    ALTER DATABASE `tpch50` SET TIFLASH REPLICA 2;
    ```

-   データベース`tpch50`用に作成された TiFlash レプリカを削除します。

    
    ```sql
    ALTER DATABASE `tpch50` SET TIFLASH REPLICA 0;
    ```

> **ノート：**
>
> -   このステートメントは、リソースを集中的に使用する一連の DDL 操作を実際に実行します。実行中にステートメントが中断された場合、実行された操作はロールバックされず、実行されていない操作は続行されません。
>
> -   ステートメントを実行した後、このデータベース**内のすべてのテーブルがレプリケートされる**まで、TiFlash レプリカの数を設定したり、このデータベースで DDL 操作を実行したりしないでください。そうしないと、次のような予期しない結果が発生する可能性があります。
>     -   TiFlash レプリカの数を 2 に設定し、データベース内のすべてのテーブルが複製される前に数を 1 に変更した場合、すべてのテーブルの TiFlash レプリカの最終的な数は必ずしも 1 または 2 とは限りません。
>     -   ステートメントの実行後、ステートメントの実行が完了する前にこのデータベースにテーブルを作成すると、これらの新しいテーブルに対して TiFlash レプリカ**が作成される場合と作成されない場合があり**ます。
>     -   ステートメントの実行後、ステートメントの実行が完了する前にデータベース内のテーブルのインデックスを追加すると、ステートメントがハングし、インデックスが追加された後にのみ再開される場合があります。
>
> -   このステートメントは、システム テーブル、ビュー、一時テーブル、および TiFlash でサポートされていない文字セットを含むテーブルをスキップします。

#### レプリケーションの進行状況を確認する {#check-replication-progress}

テーブルの TiFlash レプリカの作成と同様に、DDL ステートメントの実行が成功しても、レプリケーションが完了するわけではありません。次の SQL ステートメントを実行して、ターゲット テーブルでのレプリケーションの進行状況を確認できます。


```sql
SELECT * FROM information_schema.tiflash_replica WHERE TABLE_SCHEMA = '<db_name>';
```

データベースに TiFlash レプリカがないテーブルをチェックするには、次の SQL ステートメントを実行します。


```sql
SELECT TABLE_NAME FROM information_schema.tables where TABLE_SCHEMA = "<db_name>" and TABLE_NAME not in (SELECT TABLE_NAME FROM information_schema.tiflash_replica where TABLE_SCHEMA = "<db_name>");
```

### 利用可能なゾーンを設定する {#set-available-zones}

レプリカを構成するときに、災害復旧のために TiFlash レプリカを複数のデータ センターに配布する必要がある場合は、次の手順に従って使用可能なゾーンを構成できます。

1.  クラスター構成ファイルで TiFlash ノードのラベルを指定します。

    ```
    tiflash_servers:
      - host: 172.16.5.81
        config:
          flash.proxy.labels: zone=z1
      - host: 172.16.5.82
        config:
          flash.proxy.labels: zone=z1
      - host: 172.16.5.85
        config:
          flash.proxy.labels: zone=z2
    ```

2.  クラスターを開始した後、レプリカを作成するときにラベルを指定します。

    
    ```sql
    ALTER TABLE table_name SET TIFLASH REPLICA count LOCATION LABELS location_labels;
    ```

    例えば：

    
    ```sql
    ALTER TABLE t SET TIFLASH REPLICA 2 LOCATION LABELS "zone";
    ```

3.  PD は、ラベルに基づいてレプリカをスケジュールします。この例では、PD はそれぞれテーブル`t`の 2 つのレプリカを 2 つの使用可能なゾーンにスケジュールします。 pd-ctl を使用してスケジュールを表示できます。

    ```shell
    > tiup ctl:<version> pd -u<pd-host>:<pd-port> store

        ...
        "address": "172.16.5.82:23913",
        "labels": [
          { "key": "engine", "value": "tiflash"},
          { "key": "zone", "value": "z1" }
        ],
        "region_count": 4,

        ...
        "address": "172.16.5.81:23913",
        "labels": [
          { "key": "engine", "value": "tiflash"},
          { "key": "zone", "value": "z1" }
        ],
        "region_count": 5,
        ...

        "address": "172.16.5.85:23913",
        "labels": [
          { "key": "engine", "value": "tiflash"},
          { "key": "zone", "value": "z2" }
        ],
        "region_count": 9,
        ...
    ```

ラベルを使用してレプリカをスケジュールする方法の詳細については、 [トポロジ ラベルごとにレプリカをスケジュールする](/schedule-replicas-by-topology-labels.md) 、 [1 つの地域展開における複数のデータセンター](/multi-data-centers-in-one-city-deployment.md) 、および[2 つの都市に配置された 3 つのデータ センター](/three-data-centers-in-two-cities-deployment.md)を参照してください。

## TiDB を使用して TiFlash レプリカを読み取る {#use-tidb-to-read-tiflash-replicas}

TiDB は、TiFlash レプリカを読み取る 3 つの方法を提供します。エンジン構成なしで TiFlash レプリカを追加した場合、CBO (コストベースの最適化) モードがデフォルトで使用されます。

### スマートセレクション {#smart-selection}

TiFlash レプリカを含むテーブルの場合、TiDB オプティマイザは、コストの見積もりに基づいて TiFlash レプリカを使用するかどうかを自動的に決定します。 `desc`または`explain analyze`ステートメントを使用して、TiFlash レプリカが選択されているかどうかを確認できます。例えば：


```sql
desc select count(*) from test.t;
```

```
+--------------------------+---------+--------------+---------------+--------------------------------+
| id                       | estRows | task         | access object | operator info                  |
+--------------------------+---------+--------------+---------------+--------------------------------+
| StreamAgg_9              | 1.00    | root         |               | funcs:count(1)->Column#4       |
| └─TableReader_17         | 1.00    | root         |               | data:TableFullScan_16          |
|   └─TableFullScan_16     | 1.00    | cop[tiflash] | table:t       | keep order:false, stats:pseudo |
+--------------------------+---------+--------------+---------------+--------------------------------+
3 rows in set (0.00 sec)
```


```sql
explain analyze select count(*) from test.t;
```

```
+--------------------------+---------+---------+--------------+---------------+----------------------------------------------------------------------+--------------------------------+-----------+------+
| id                       | estRows | actRows | task         | access object | execution info                                                       | operator info                  | memory    | disk |
+--------------------------+---------+---------+--------------+---------------+----------------------------------------------------------------------+--------------------------------+-----------+------+
| StreamAgg_9              | 1.00    | 1       | root         |               | time:83.8372ms, loops:2                                              | funcs:count(1)->Column#4       | 372 Bytes | N/A  |
| └─TableReader_17         | 1.00    | 1       | root         |               | time:83.7776ms, loops:2, rpc num: 1, rpc time:83.5701ms, proc keys:0 | data:TableFullScan_16          | 152 Bytes | N/A  |
|   └─TableFullScan_16     | 1.00    | 1       | cop[tiflash] | table:t       | time:43ms, loops:1                                                   | keep order:false, stats:pseudo | N/A       | N/A  |
+--------------------------+---------+---------+--------------+---------------+----------------------------------------------------------------------+--------------------------------+-----------+------+
```

`cop[tiflash]`は、タスクが処理のために TiFlash に送信されることを意味します。 TiFlash レプリカを選択していない場合は、 `analyze table`ステートメントを使用して統計を更新してから、 `explain analyze`ステートメントを使用して結果を確認できます。

テーブルに TiFlash レプリカが 1 つしかなく、関連するノードがサービスを提供できない場合、CBO モードのクエリは繰り返し再試行されることに注意してください。この状況では、エンジンを指定するか、手動ヒントを使用して TiKV レプリカからデータを読み取る必要があります。

### エンジンの分離 {#engine-isolation}

エンジンの分離とは、対応する変数を構成することにより、すべてのクエリが指定されたエンジンのレプリカを使用することを指定することです。オプションのエンジンは、「tikv」、「tidb」(一部の TiDB システム テーブルを格納し、ユーザーが積極的に使用できない TiDB の内部メモリ テーブル領域を示す)、および「tiflash」であり、次の 2 つの構成レベルがあります。

-   TiDB インスタンス レベル、つまり INSTANCE レベル。 TiDB 構成ファイルに次の構成項目を追加します。

    ```
    [isolation-read]
    engines = ["tikv", "tidb", "tiflash"]
    ```

    **INSTANCE レベルのデフォルト設定は`[&quot;tikv&quot;, &quot;tidb&quot;, &quot;tiflash&quot;]`です。**

-   セッションレベル。次のステートメントを使用して構成します。

    
    ```sql
    set @@session.tidb_isolation_read_engines = "engine list separated by commas";
    ```

    また

    
    ```sql
    set SESSION tidb_isolation_read_engines = "engine list separated by commas";
    ```

    SESSION レベルのデフォルト構成は、TiDB INSTANCE レベルの構成を継承しています。

最終的なエンジン構成はセッション レベルの構成です。つまり、セッション レベルの構成がインスタンス レベルの構成をオーバーライドします。たとえば、INSTANCE レベルで「tikv」を構成し、SESSION レベルで「tiflash」を構成した場合、TiFlash レプリカが読み取られます。最終的なエンジン構成が「tikv」および「tiflash」である場合、TiKV および TiFlash レプリカの両方が読み取られ、オプティマイザーは実行するより適切なエンジンを自動的に選択します。

> **ノート：**
>
> [TiDB ダッシュボード](/dashboard/dashboard-intro.md)およびその他のコンポーネントは、TiDB メモリ テーブル領域に格納されているシステム テーブルを読み取る必要があるため、インスタンス レベルのエンジン構成に常に「tidb」エンジンを追加することをお勧めします。

照会されたテーブルに指定されたエンジンのレプリカがない場合 (たとえば、エンジンが「tiflash」として構成されているが、テーブルに TiFlash レプリカがない場合)、クエリはエラーを返します。

### 手動ヒント {#manual-hint}

手動ヒントは、TiDB が特定のテーブルに対して指定されたレプリカを使用するように強制することができます。手動ヒントの使用例を次に示します。


```sql
select /*+ read_from_storage(tiflash[table_name]) */ ... from table_name;
```

クエリ ステートメントでテーブルにエイリアスを設定する場合、ヒントを有効にするには、ヒントを含むステートメントでエイリアスを使用する必要があります。例えば：


```sql
select /*+ read_from_storage(tiflash[alias_a,alias_b]) */ ... from table_name_1 as alias_a, table_name_2 as alias_b where alias_a.column_1 = alias_b.column_2;
```

上記のステートメントで、 `tiflash[]`はオプティマイザーに TiFlash レプリカを読み取るように促します。 `tikv[]`を使用して、オプティマイザーに必要に応じて TiKV レプリカを読み取るように指示することもできます。ヒント構文の詳細については、 [READ_FROM_STORAGE](/optimizer-hints.md#read_from_storagetiflasht1_name--tl_name--tikvt2_name--tl_name-)を参照してください。

ヒントで指定されたテーブルに指定されたエンジンのレプリカがない場合、ヒントは無視され、警告が報告されます。また、ヒントはエンジン分離前提でのみ有効です。ヒントで指定されたエンジンがエンジン分離リストにない場合、ヒントも無視され、警告が報告されます。

> **ノート：**
>
> 5.7.7 以前のバージョンの MySQL クライアントは、デフォルトでオプティマイザ ヒントをクリアします。これらの初期バージョンでヒント構文を使用するには、クライアントを`--comments`オプション (例: `mysql -h 127.0.0.1 -P 4000 -uroot --comments` ) で起動します。

### スマートセレクション、エンジンアイソレーション、マニュアルヒントの関係 {#the-relationship-of-smart-selection-engine-isolation-and-manual-hint}

上記の 3 つの TiFlash レプリカの読み取り方法では、エンジンの分離により、エンジンの使用可能なレプリカの全体的な範囲が指定されます。この範囲内では、手動ヒントにより、よりきめ細かいステートメント レベルおよびテーブル レベルのエンジン選択が提供されます。最後に、CBO が決定を下し、指定されたエンジン リスト内のコスト見積もりに基づいてエンジンのレプリカを選択します。

> **ノート：**
>
> v4.0.3 より前では、読み取り専用ではない SQL ステートメント (たとえば、 `INSERT INTO ... SELECT` 、 `SELECT ... FOR UPDATE` 、 `UPDATE ...` 、 `DELETE ...` ) での TiFlash レプリカからの読み取りの動作は定義されていません。 v4.0.3 以降のバージョンでは、データの正確性を保証するために、TiDB は非読み取り専用 SQL ステートメントの TiFlash レプリカを内部的に無視します。つまり、 [スマートセレクション](#smart-selection)の場合、TiDB は非 TiFlash レプリカを自動的に選択します。 TiFlash レプリカ**のみ**を指定する[エンジン分離](#engine-isolation)の場合、TiDB はエラーを報告します。 [手動ヒント](#manual-hint)の場合、TiDB はヒントを無視します。

## TiSpark を使用して TiFlash レプリカを読み取る {#use-tispark-to-read-tiflash-replicas}

現在、TiSpark を使用して、TiDB のエンジン分離と同様の方法で TiFlash レプリカを読み取ることができます。このメソッドは、 `spark.tispark.isolation_read_engines`つのパラメーターを構成することです。パラメータ値のデフォルトは`tikv,tiflash`です。これは、CBO の選択に従って、TiDB が TiFlash または TiKV からデータを読み取ることを意味します。パラメータ値を`tiflash`に設定すると、TiDB が強制的に TiFlash からデータを読み取ることを意味します。

> **ノート**
>
> このパラメーターが`tiflash`に設定されている場合、クエリに含まれるすべてのテーブルの TiFlash レプリカのみが読み取られ、これらのテーブルには TiFlash レプリカが必要です。 TiFlash レプリカを持たないテーブルの場合、エラーが報告されます。このパラメーターを`tikv`に設定すると、TiKV レプリカのみが読み取られます。

このパラメーターは、次のいずれかの方法で構成できます。

-   `spark-defaults.conf`ファイルに次の項目を追加します。

    ```
    spark.tispark.isolation_read_engines tiflash
    ```

-   Spark シェルまたは Thriftサーバーを初期化するときに、初期化コマンドに`--conf spark.tispark.isolation_read_engines=tiflash`を追加します。

-   リアルタイムで Spark シェルに`spark.conf.set("spark.tispark.isolation_read_engines", "tiflash")`を設定します。

-   サーバーがビーライン経由で接続された後、Thriftサーバーに`set spark.tispark.isolation_read_engines=tiflash`を設定します。

## サポートされているプッシュダウン計算 {#supported-push-down-calculations}

TiFlash は、次の演算子のプッシュダウンをサポートしています。

-   TableScan: テーブルからデータを読み取ります。
-   選択: データをフィルタリングします。
-   HashAgg: [ハッシュ集計](/explain-aggregation.md#hash-aggregation)アルゴリズムに基づいてデータ集計を実行します。
-   StreamAgg: [ストリーム集計](/explain-aggregation.md#stream-aggregation)アルゴリズムに基づいてデータ集計を実行します。 SteamAgg は`GROUP BY`条件なしの集計のみをサポートします。
-   TopN: TopN 計算を実行します。
-   Limit: リミット計算を実行します。
-   Project: 投影計算を実行します。
-   HashJoin: [ハッシュ結合](/explain-joins.md#hash-join)アルゴリズムを使用して結合計算を実行しますが、次の条件があります。
    -   オペレーターは[MPP モード](#use-the-mpp-mode)でのみ押し下げることができます。
    -   サポートされている結合は、Inner Join、Left Join、Semi Join、Anti Semi Join、Left Semi Join、Anti Left Semi Join です。
    -   上記の結合は、等結合と非等結合 (デカルト結合) の両方をサポートしています。 Cartesian Join を計算する場合、Shuffle Hash Join アルゴリズムの代わりに Broadcast アルゴリズムが使用されます。
-   ウィンドウ関数: 現在、TiFlash は row_number()、rank()、dense_rank() をサポートしています。

TiDB では、オペレーターはツリー構造で編成されています。オペレータが TiFlash にプッシュされるには、次のすべての前提条件を満たす必要があります。

-   その子オペレーターはすべて TiFlash にプッシュダウンできます。
-   演算子に式が含まれている場合 (ほとんどの演算子には式が含まれています)、演算子のすべての式を TiFlash にプッシュできます。

現在、TiFlash は次のプッシュダウン式をサポートしています。

-   数学関数: `+, -, /, *, %, >=, <=, =, !=, <, >, round, abs, floor(int), ceil(int), ceiling(int), sqrt, log, log2, log10, ln, exp, pow, sign, radians, degrees, conv, crc32, greatest(int/real), least(int/real)`
-   論理関数: `and, or, not, case when, if, ifnull, isnull, in, like, coalesce, is`
-   ビット演算: `bitand, bitor, bigneg, bitxor`
-   文字列関数: `substr, char_length, replace, concat, concat_ws, left, right, ascii, length, trim, ltrim, rtrim, position, format, lower, ucase, upper, substring_index, lpad, rpad, strcmp, regexp`
-   日付関数： `date_format, timestampdiff, from_unixtime, unix_timestamp(int), unix_timestamp(decimal), str_to_date(date), str_to_date(datetime), datediff, year, month, day, extract(datetime), date, hour, microsecond, minute, second, sysdate, date_add, date_sub, adddate, subdate, quarter, dayname, dayofmonth, dayofweek, dayofyear, last_day, monthname, to_seconds, to_days, from_days, weekofyear`
-   JSON 関数: `json_length`
-   変換関数： `cast(int as double), cast(int as decimal), cast(int as string), cast(int as time), cast(double as int), cast(double as decimal), cast(double as string), cast(double as time), cast(string as int), cast(string as double), cast(string as decimal), cast(string as time), cast(decimal as int), cast(decimal as string), cast(decimal as time), cast(time as int), cast(time as decimal), cast(time as string), cast(time as real)`
-   集計関数: `min, max, sum, count, avg, approx_count_distinct, group_concat`
-   その他の関数: `inetntoa, inetaton, inet6ntoa, inet6aton`

### その他の制限 {#other-restrictions}

-   Bit、Set、および Geometry タイプを含む式は、TiFlash にプッシュダウンできません。

-   `date_add` 、 `date_sub` 、 `adddate` 、および`subdate`関数は、次の間隔タイプのみをサポートします。他の間隔タイプが使用されている場合、TiFlash はエラーを報告します。

    -   日
    -   週
    -   月
    -   年
    -   時間
    -   分
    -   2番目

サポートされていないプッシュダウン計算がクエリで発生した場合、TiDB は残りの計算を完了する必要があり、TiFlash アクセラレーション効果に大きな影響を与える可能性があります。現在サポートされていない演算子と式は、将来のバージョンでサポートされる可能性があります。

## MPP モードを使用する {#use-the-mpp-mode}

TiFlash は、MPP モードを使用してクエリを実行することをサポートしています。これにより、クロスノード データ交換 (データ シャッフル プロセス) が計算に導入されます。 TiDB は、オプティマイザのコスト見積もりを使用して、MPP モードを選択するかどうかを自動的に決定します。 [`tidb_allow_mpp`](/system-variables.md#tidb_allow_mpp-new-in-v50)と[`tidb_enforce_mpp`](/system-variables.md#tidb_enforce_mpp-new-in-v51)の値を変更することで、選択戦略を変更できます。

### MPP モードを選択するかどうかを制御します {#control-whether-to-select-the-mpp-mode}

`tidb_allow_mpp`変数は、TiDB が MPP モードを選択してクエリを実行できるかどうかを制御します。 `tidb_enforce_mpp`変数は、オプティマイザーのコスト見積もりを無視し、TiFlash の MPP モードを強制的に使用してクエリを実行するかどうかを制御します。

これら 2 つの変数のすべての値に対応する結果は次のとおりです。

|                              | tidb_allow_mpp=オフ | tidb_allow_mpp=on (デフォルト)                     |
| ---------------------------- | ----------------- | --------------------------------------------- |
| tidb_enforce_mpp=off (デフォルト) | MPP モードは使用されません。  | オプティマイザは、コストの見積もりに基づいて MPP モードを選択します。 (デフォルト) |
| tidb_enforce_mpp=オン          | MPP モードは使用されません。  | TiDB はコスト見積もりを無視し、MPP モードを選択します。              |

たとえば、MPP モードを使用したくない場合は、次のステートメントを実行できます。


```sql
set @@session.tidb_allow_mpp=1;
set @@session.tidb_enforce_mpp=0;
```

TiDB のコストベースのオプティマイザに、MPP モードを使用するかどうか (デフォルトで) を自動的に決定させたい場合は、次のステートメントを実行できます。


```sql
set @@session.tidb_allow_mpp=1;
set @@session.tidb_enforce_mpp=0;
```

TiDB にオプティマイザーのコスト見積もりを無視させ、MPP モードを強制的に選択させたい場合は、次のステートメントを実行できます。


```sql
set @@session.tidb_allow_mpp=1;
set @@session.tidb_enforce_mpp=1;
```

`tidb_enforce_mpp`セッション変数の初期値は、この tidb-server インスタンスの[`enforce-mpp`](/tidb-configuration-file.md#enforce-mpp)構成値 (デフォルトでは`false` ) と同じです。 TiDB クラスター内の複数の tidb-server インスタンスが分析クエリのみを実行し、これらのインスタンスで MPP モードが使用されていることを確認したい場合は、それらの[`enforce-mpp`](/tidb-configuration-file.md#enforce-mpp)構成値を`true`に変更できます。

> **ノート：**
>
> `tidb_enforce_mpp=1`が有効になると、TiDB オプティマイザーはコスト見積もりを無視して MPP モードを選択します。ただし、他の要因が MPP モードをブロックする場合、TiDB は MPP モードを選択しません。これらの要因には、TiFlash レプリカの不在、TiFlash レプリカの未完成の複製、および MPP モードでサポートされていない演算子または関数を含むステートメントが含まれます。
>
> コスト見積もり以外の理由で TiDB オプティマイザーが MPP モードを選択できない場合、 `EXPLAIN`ステートメントを使用して実行計画をチェックアウトすると、その理由を説明する警告が返されます。例えば：
>
> >
> ```sql
> set @@session.tidb_enforce_mpp=1;
> create table t(a int);
> explain select count(*) from t;
> show warnings;
> ```
>
> ```
> +---------+------+-----------------------------------------------------------------------------+
> | Level   | Code | Message                                                                     |
> +---------+------+-----------------------------------------------------------------------------+
> | Warning | 1105 | MPP mode may be blocked because there aren't tiflash replicas of table `t`. |
> +---------+------+-----------------------------------------------------------------------------+
> ```

### MPP モードのアルゴリズム サポート {#algorithm-support-for-the-mpp-mode}

MPP モードは、ブロードキャスト ハッシュ結合、シャッフル ハッシュ結合、シャッフル ハッシュ集計、Union All、TopN、および Limit の物理アルゴリズムをサポートします。オプティマイザは、クエリで使用するアルゴリズムを自動的に決定します。特定のクエリ実行プランを確認するには、 `EXPLAIN`ステートメントを実行します。 `EXPLAIN`ステートメントの結果が ExchangeSender および ExchangeReceiver オペレーターを示している場合、MPP モードが有効になっていることを示します。

次のステートメントは、例として TPC-H テスト セットのテーブル構造を取ります。

```sql
explain select count(*) from customer c join nation n on c.c_nationkey=n.n_nationkey;
+------------------------------------------+------------+-------------------+---------------+----------------------------------------------------------------------------+
| id                                       | estRows    | task              | access object | operator info                                                              |
+------------------------------------------+------------+-------------------+---------------+----------------------------------------------------------------------------+
| HashAgg_23                               | 1.00       | root              |               | funcs:count(Column#16)->Column#15                                          |
| └─TableReader_25                         | 1.00       | root              |               | data:ExchangeSender_24                                                     |
|   └─ExchangeSender_24                    | 1.00       | batchCop[tiflash] |               | ExchangeType: PassThrough                                                  |
|     └─HashAgg_12                         | 1.00       | batchCop[tiflash] |               | funcs:count(1)->Column#16                                                  |
|       └─HashJoin_17                      | 3000000.00 | batchCop[tiflash] |               | inner join, equal:[eq(tpch.nation.n_nationkey, tpch.customer.c_nationkey)] |
|         ├─ExchangeReceiver_21(Build)     | 25.00      | batchCop[tiflash] |               |                                                                            |
|         │ └─ExchangeSender_20            | 25.00      | batchCop[tiflash] |               | ExchangeType: Broadcast                                                    |
|         │   └─TableFullScan_18           | 25.00      | batchCop[tiflash] | table:n       | keep order:false                                                           |
|         └─TableFullScan_22(Probe)        | 3000000.00 | batchCop[tiflash] | table:c       | keep order:false                                                           |
+------------------------------------------+------------+-------------------+---------------+----------------------------------------------------------------------------+
9 rows in set (0.00 sec)
```

実行計画の例では、 `ExchangeReceiver`と`ExchangeSender`の演算子が含まれています。実行計画は、 `nation`テーブルが読み取られた後、 `ExchangeSender`オペレーターがテーブルを各ノードにブロードキャストし、 `nation`テーブルと`customer`テーブルに対して`HashJoin`と`HashAgg`の操作が実行され、結果が TiDB に返されることを示しています。

TiFlash は、ブロードキャスト ハッシュ結合を使用するかどうかを制御するために、次の 2 つのグローバル/セッション変数を提供します。

-   [`tidb_broadcast_join_threshold_size`](/system-variables.md#tidb_broadcast_join_threshold_count-new-in-v50) : 値の単位はバイトです。テーブル サイズ (バイト単位) が変数の値より小さい場合は、ブロードキャスト ハッシュ結合アルゴリズムが使用されます。それ以外の場合は、Shuffled Hash Join アルゴリズムが使用されます。
-   [`tidb_broadcast_join_threshold_count`](/system-variables.md#tidb_broadcast_join_threshold_count-new-in-v50) : 値の単位は行です。結合操作のオブジェクトがサブクエリに属している場合、オプティマイザはサブクエリの結果セットのサイズを見積もることができないため、サイズは結果セットの行数によって決定されます。サブクエリの推定行数がこの変数の値より少ない場合、ブロードキャスト ハッシュ結合アルゴリズムが使用されます。それ以外の場合は、Shuffled Hash Join アルゴリズムが使用されます。

## MPP モードで分割されたテーブルにアクセスする {#access-partitioned-tables-in-the-mpp-mode}

MPP モードで分割されたテーブルにアクセスするには、最初に[動的プルーニング モード](https://docs.pingcap.com/tidb/stable/partitioned-table#dynamic-pruning-mode)を有効にする必要があります。

例：

```sql
mysql> DROP TABLE if exists test.employees;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> CREATE TABLE test.employees
(id int(11) NOT NULL,
 fname varchar(30) DEFAULT NULL,
 lname varchar(30) DEFAULT NULL,
 hired date NOT NULL DEFAULT '1970-01-01',
 separated date DEFAULT '9999-12-31',
 job_code int DEFAULT NULL,
 store_id int NOT NULL) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
PARTITION BY RANGE (store_id)
(PARTITION p0 VALUES LESS THAN (6),
 PARTITION p1 VALUES LESS THAN (11),
 PARTITION p2 VALUES LESS THAN (16),
 PARTITION p3 VALUES LESS THAN (MAXVALUE));
Query OK, 0 rows affected (0.10 sec)

mysql> ALTER table test.employees SET tiflash replica 1;
Query OK, 0 rows affected (0.09 sec)

mysql> SET tidb_partition_prune_mode=static;
Query OK, 0 rows affected (0.00 sec)

mysql> explain SELECT count(*) FROM test.employees;
+----------------------------------+----------+-------------------+-------------------------------+-----------------------------------+
| id                               | estRows  | task              | access object                 | operator info                     |
+----------------------------------+----------+-------------------+-------------------------------+-----------------------------------+
| HashAgg_18                       | 1.00     | root              |                               | funcs:count(Column#10)->Column#9  |
| └─PartitionUnion_20              | 4.00     | root              |                               |                                   |
|   ├─StreamAgg_35                 | 1.00     | root              |                               | funcs:count(Column#12)->Column#10 |
|   │ └─TableReader_36             | 1.00     | root              |                               | data:StreamAgg_26                 |
|   │   └─StreamAgg_26             | 1.00     | batchCop[tiflash] |                               | funcs:count(1)->Column#12         |
|   │     └─TableFullScan_34       | 10000.00 | batchCop[tiflash] | table:employees, partition:p0 | keep order:false, stats:pseudo    |
|   ├─StreamAgg_52                 | 1.00     | root              |                               | funcs:count(Column#14)->Column#10 |
|   │ └─TableReader_53             | 1.00     | root              |                               | data:StreamAgg_43                 |
|   │   └─StreamAgg_43             | 1.00     | batchCop[tiflash] |                               | funcs:count(1)->Column#14         |
|   │     └─TableFullScan_51       | 10000.00 | batchCop[tiflash] | table:employees, partition:p1 | keep order:false, stats:pseudo    |
|   ├─StreamAgg_69                 | 1.00     | root              |                               | funcs:count(Column#16)->Column#10 |
|   │ └─TableReader_70             | 1.00     | root              |                               | data:StreamAgg_60                 |
|   │   └─StreamAgg_60             | 1.00     | batchCop[tiflash] |                               | funcs:count(1)->Column#16         |
|   │     └─TableFullScan_68       | 10000.00 | batchCop[tiflash] | table:employees, partition:p2 | keep order:false, stats:pseudo    |
|   └─StreamAgg_86                 | 1.00     | root              |                               | funcs:count(Column#18)->Column#10 |
|     └─TableReader_87             | 1.00     | root              |                               | data:StreamAgg_77                 |
|       └─StreamAgg_77             | 1.00     | batchCop[tiflash] |                               | funcs:count(1)->Column#18         |
|         └─TableFullScan_85       | 10000.00 | batchCop[tiflash] | table:employees, partition:p3 | keep order:false, stats:pseudo    |
+----------------------------------+----------+-------------------+-------------------------------+-----------------------------------+
18 rows in set (0,00 sec)

mysql> SET tidb_partition_prune_mode=dynamic;
Query OK, 0 rows affected (0.00 sec)

mysql> explain SELECT count(*) FROM test.employees;
+------------------------------+----------+--------------+-----------------+---------------------------------------------------------+
| id                           | estRows  | task         | access object   | operator info                                           |
+------------------------------+----------+--------------+-----------------+---------------------------------------------------------+
| HashAgg_17                   | 1.00     | root         |                 | funcs:count(Column#11)->Column#9                        |
| └─TableReader_19             | 1.00     | root         | partition:all   | data:ExchangeSender_18                                  |
|   └─ExchangeSender_18        | 1.00     | mpp[tiflash] |                 | ExchangeType: PassThrough                               |
|     └─HashAgg_8              | 1.00     | mpp[tiflash] |                 | funcs:count(1)->Column#11                               |
|       └─TableFullScan_16     | 10000.00 | mpp[tiflash] | table:employees | keep order:false, stats:pseudo, PartitionTableScan:true |
+------------------------------+----------+--------------+-----------------+---------------------------------------------------------+
5 rows in set (0,00 sec)
```

## データ検証 {#data-validation}

### ユーザー シナリオ {#user-scenarios}

通常、データの破損は深刻なハードウェア障害によって引き起こされます。このような場合、手動でデータを回復しようとしても、データの信頼性が低下します。

データの整合性を確保するために、デフォルトでは、TiFlash は`City128`アルゴリズムを使用して、データ ファイルに対して基本的なデータ検証を実行します。データの検証に失敗した場合、TiFlash はすぐにエラーを報告して終了し、データの不一致による二次災害を回避します。この時点で、TiFlash ノードを復元する前に、手動で介入してデータを再度複製する必要があります。

v5.4.0 から、TiFlash はより高度なデータ検証機能を導入しています。 TiFlash はデフォルトで`XXH3`アルゴリズムを使用し、検証フレームとアルゴリズムをカスタマイズできます。

### 検証メカニズム {#validation-mechanism}

検証メカニズムは、DeltaTree ファイル (DTFile) に基づいています。 DTFile は、TiFlash データを保持するストレージ ファイルです。 DTFile には次の 3 つの形式があります。

| バージョン | 州                         | 検証メカニズム                                                      | ノート                         |
| :---- | :------------------------ | :----------------------------------------------------------- | :-------------------------- |
| V1    | 非推奨                       | ハッシュはデータ ファイルに埋め込まれます。                                       |                             |
| V2    | バージョン &lt; v6.0.0 のデフォルト  | ハッシュはデータ ファイルに埋め込まれます。                                       | V1 と比較して、V2 は列データの統計を追加します。 |
| V3    | バージョン &gt;= v6.0.0 のデフォルト | V3 には、メタデータとトークン データのチェックサムが含まれており、複数のハッシュ アルゴリズムをサポートしています。 | v5.4.0 の新機能。                |

DTFile はデータファイルディレクトリの`stable`フォルダに格納されています。現在有効になっている形式はすべてフォルダー形式です。つまり、データは`dmf_<file id>`のような名前のフォルダーの下にある複数のファイルに保存されます。

#### データ検証を使用する {#use-data-validation}

TiFlash は、自動データ検証と手動データ検証の両方をサポートしています。

-   自動データ検証:
    -   v6.0.0 以降のバージョンでは、デフォルトで V3 検証メカニズムが使用されます。
    -   v6.0.0 より前のバージョンでは、デフォルトで V2 検証メカニズムが使用されます。
    -   検証メカニズムを手動で切り替えるには、 [TiFlash 構成ファイル](/tiflash/tiflash-configuration.md#configure-the-tiflashtoml-file)を参照してください。ただし、デフォルト構成はテストによって検証されているため、推奨されます。
-   手動データ検証。 [`DTTool inspect`](/tiflash/tiflash-command-line-flags.md#dttool-inspect)を参照してください。

> **警告：**
>
> V3 検証メカニズムを有効にすると、新しく生成された DTFile を v5.4.0 より前の TiFlash で直接読み取ることができなくなります。 v5.4.0 以降、TiFlash は V2 と V3 の両方をサポートし、積極的にバージョンをアップグレードまたはダウングレードしません。既存のファイルのバージョンをアップグレードまたはダウングレードする必要がある場合は、手動で行う必要があります[バージョンを切り替える](/tiflash/tiflash-command-line-flags.md#dttool-migrate) 。

#### 検証ツール {#validation-tool}

TiFlash がデータを読み取るときに実行される自動データ検証に加えて、データの整合性を手動でチェックするツールが v5.4.0 で導入されました。詳細については、 [DTツール](/tiflash/tiflash-command-line-flags.md#dttool-inspect)を参照してください。

## ノート {#notes}

次の状況では、TiFlash は TiDB と互換性がありません。

-   TiFlash 計算レイヤーでは:
    -   オーバーフローした数値のチェックはサポートされていません。たとえば、 `BIGINT`タイプ`9223372036854775807 + 9223372036854775807`の 2 つの最大値を加算します。 TiDB でのこの計算の予想される動作は、 `ERROR 1690 (22003): BIGINT value is out of range`エラーを返すことです。ただし、この計算を TiFlash で実行すると、オーバーフロー値`-2`がエラーなしで返されます。
    -   ウィンドウ関数はサポートされていません。
    -   TiKV からのデータの読み取りはサポートされていません。
    -   現在、TiFlash の`sum`関数は文字列型の引数をサポートしていません。しかし、TiDB は、コンパイル中に文字列型の引数が`sum`関数に渡されたかどうかを識別できません。したがって、 `select sum(string_col) from t`のようなステートメントを実行すると、TiFlash は`[FLASH:Coprocessor:Unimplemented] CastStringAsReal is not supported.`エラーを返します。このようなエラーを回避するには、この SQL ステートメントを`select sum(cast(string_col as double)) from t`に変更する必要があります。
    -   現在、TiFlash の 10 進数除算の計算は TiDB のそれと互換性がありません。たとえば、10 進数を除算する場合、TiFlash は常にコンパイルから推測された型を使用して計算を実行します。ただし、TiDB は、コンパイルから推測される型よりも正確な型を使用して、この計算を実行します。そのため、10 進数除算を含む一部の SQL 文は、TiDB + TiKV と TiDB + TiFlash で実行した場合に異なる実行結果を返します。例えば：

        ```sql
        mysql> create table t (a decimal(3,0), b decimal(10, 0));
        Query OK, 0 rows affected (0.07 sec)
        mysql> insert into t values (43, 1044774912);
        Query OK, 1 row affected (0.03 sec)
        mysql> alter table t set tiflash replica 1;
        Query OK, 0 rows affected (0.07 sec)
        mysql> set session tidb_isolation_read_engines='tikv';
        Query OK, 0 rows affected (0.00 sec)
        mysql> select a/b, a/b + 0.0000000000001 from t where a/b;
        +--------+-----------------------+
        | a/b    | a/b + 0.0000000000001 |
        +--------+-----------------------+
        | 0.0000 |       0.0000000410001 |
        +--------+-----------------------+
        1 row in set (0.00 sec)
        mysql> set session tidb_isolation_read_engines='tiflash';
        Query OK, 0 rows affected (0.00 sec)
        mysql> select a/b, a/b + 0.0000000000001 from t where a/b;
        Empty set (0.01 sec)
        ```

        上記の例では、コンパイルから推測される`a/b`の型は、TiDB と TiFlash の両方で`Decimal(7,4)`です。 `Decimal(7,4)`によって制約され、 `a/b`の返される型は`0.0000`である必要があります。 TiDB では、 `a/b`の実行時の精度が`Decimal(7,4)`よりも高いため、元のテーブル データは`where a/b`の条件によってフィルター処理されません。ただし、TiFlash では、 `a/b`の計算は結果の型として`Decimal(7,4)`を使用するため、元のテーブル データは`where a/b`の条件でフィルター処理されます。
