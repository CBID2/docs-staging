---
title: SLOW_QUERY
summary: Learn the `SLOW_QUERY` INFORMATION_SCHEMA table.
---

# SLOW_QUERY {#slow-query}

`SLOW_QUERY`テーブルは、TiDB スロー ログ ファイルの解析結果である、現在のノードのスロー クエリ情報を提供します。テーブル内の列名は、スロー ログ内のフィールド名に対応しています。

> **注記：**
>
> このテーブルは[TiDB サーバーレス](https://docs.pingcap.com/tidbcloud/select-cluster-tier#tidb-serverless)クラスターでは使用できません。

<CustomContent platform="tidb">

このテーブルを使用して問題のあるステートメントを特定し、クエリのパフォーマンスを向上させる方法については、 [スロークエリログドキュメント](/identify-slow-queries.md)を参照してください。

</CustomContent>

```sql
USE INFORMATION_SCHEMA;
DESC SLOW_QUERY;
```

出力は次のとおりです。

```sqlsql
+-------------------------------+---------------------+------+------+---------+-------+
| Field                         | Type                | Null | Key  | Default | Extra |
+-------------------------------+---------------------+------+------+---------+-------+
| Time                          | timestamp(6)        | NO   | PRI  | NULL    |       |
| Txn_start_ts                  | bigint(20) unsigned | YES  |      | NULL    |       |
| User                          | varchar(64)         | YES  |      | NULL    |       |
| Host                          | varchar(64)         | YES  |      | NULL    |       |
| Conn_ID                       | bigint(20) unsigned | YES  |      | NULL    |       |
| Exec_retry_count              | bigint(20) unsigned | YES  |      | NULL    |       |
| Exec_retry_time               | double              | YES  |      | NULL    |       |
| Query_time                    | double              | YES  |      | NULL    |       |
| Parse_time                    | double              | YES  |      | NULL    |       |
| Compile_time                  | double              | YES  |      | NULL    |       |
| Rewrite_time                  | double              | YES  |      | NULL    |       |
| Preproc_subqueries            | bigint(20) unsigned | YES  |      | NULL    |       |
| Preproc_subqueries_time       | double              | YES  |      | NULL    |       |
| Optimize_time                 | double              | YES  |      | NULL    |       |
| Wait_TS                       | double              | YES  |      | NULL    |       |
| Prewrite_time                 | double              | YES  |      | NULL    |       |
| Wait_prewrite_binlog_time     | double              | YES  |      | NULL    |       |
| Commit_time                   | double              | YES  |      | NULL    |       |
| Get_commit_ts_time            | double              | YES  |      | NULL    |       |
| Commit_backoff_time           | double              | YES  |      | NULL    |       |
| Backoff_types                 | varchar(64)         | YES  |      | NULL    |       |
| Resolve_lock_time             | double              | YES  |      | NULL    |       |
| Local_latch_wait_time         | double              | YES  |      | NULL    |       |
| Write_keys                    | bigint(22)          | YES  |      | NULL    |       |
| Write_size                    | bigint(22)          | YES  |      | NULL    |       |
| Prewrite_region               | bigint(22)          | YES  |      | NULL    |       |
| Txn_retry                     | bigint(22)          | YES  |      | NULL    |       |
| Cop_time                      | double              | YES  |      | NULL    |       |
| Process_time                  | double              | YES  |      | NULL    |       |
| Wait_time                     | double              | YES  |      | NULL    |       |
| Backoff_time                  | double              | YES  |      | NULL    |       |
| LockKeys_time                 | double              | YES  |      | NULL    |       |
| Request_count                 | bigint(20) unsigned | YES  |      | NULL    |       |
| Total_keys                    | bigint(20) unsigned | YES  |      | NULL    |       |
| Process_keys                  | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_delete_skipped_count  | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_key_skipped_count     | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_cache_hit_count | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_read_count      | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_read_byte       | bigint(20) unsigned | YES  |      | NULL    |       |
| DB                            | varchar(64)         | YES  |      | NULL    |       |
| Index_names                   | varchar(100)        | YES  |      | NULL    |       |
| Is_internal                   | tinyint(1)          | YES  |      | NULL    |       |
| Digest                        | varchar(64)         | YES  |      | NULL    |       |
| Stats                         | varchar(512)        | YES  |      | NULL    |       |
| Cop_proc_avg                  | double              | YES  |      | NULL    |       |
| Cop_proc_p90                  | double              | YES  |      | NULL    |       |
| Cop_proc_max                  | double              | YES  |      | NULL    |       |
| Cop_proc_addr                 | varchar(64)         | YES  |      | NULL    |       |
| Cop_wait_avg                  | double              | YES  |      | NULL    |       |
| Cop_wait_p90                  | double              | YES  |      | NULL    |       |
| Cop_wait_max                  | double              | YES  |      | NULL    |       |
| Cop_wait_addr                 | varchar(64)         | YES  |      | NULL    |       |
| Mem_max                       | bigint(20)          | YES  |      | NULL    |       |
| Disk_max                      | bigint(20)          | YES  |      | NULL    |       |
| KV_total                      | double              | YES  |      | NULL    |       |
| PD_total                      | double              | YES  |      | NULL    |       |
| Backoff_total                 | double              | YES  |      | NULL    |       |
| Write_sql_response_total      | double              | YES  |      | NULL    |       |
| Result_rows                   | bigint(22)          | YES  |      | NULL    |       |
| Warnings                      | longtext            | YES  |      | NULL    |       |
| Backoff_Detail                | varchar(4096)       | YES  |      | NULL    |       |
| Prepared                      | tinyint(1)          | YES  |      | NULL    |       |
| Succ                          | tinyint(1)          | YES  |      | NULL    |       |
| IsExplicitTxn                 | tinyint(1)          | YES  |      | NULL    |       |
| IsWriteCacheTable             | tinyint(1)          | YES  |      | NULL    |       |
| Plan_from_cache               | tinyint(1)          | YES  |      | NULL    |       |
| Plan_from_binding             | tinyint(1)          | YES  |      | NULL    |       |
| Has_more_results              | tinyint(1)          | YES  |      | NULL    |       |
| Plan                          | longtext            | YES  |      | NULL    |       |
| Plan_digest                   | varchar(128)        | YES  |      | NULL    |       |
| Binary_plan                   | longtext            | YES  |      | NULL    |       |
| Prev_stmt                     | longtext            | YES  |      | NULL    |       |
| Query                         | longtext            | YES  |      | NULL    |       |
+-------------------------------+---------------------+------+------+---------+-------+
74 rows in set (0.001 sec)
```

## CLUSTER_SLOW_QUERY テーブル {#cluster-slow-query-table}

`CLUSTER_SLOW_QUERY`テーブルは、クラスター内のすべてのノードのスロー クエリ情報を提供します。これは、TiDB スロー ログ ファイルの解析結果です。 `CLUSTER_SLOW_QUERY`テーブルは`SLOW_QUERY`と同じように使用できます。 `CLUSTER_SLOW_QUERY`テーブルのテーブル スキーマは、 `INSTANCE`列が`CLUSTER_SLOW_QUERY`に追加されるという点で`SLOW_QUERY`テーブルのテーブル スキーマと異なります。 `INSTANCE`列は、スロー クエリの行情報の TiDB ノード アドレスを表します。

> **注記：**
>
> このテーブルは[TiDB サーバーレス](https://docs.pingcap.com/tidbcloud/select-cluster-tier#tidb-serverless)クラスターでは使用できません。

<CustomContent platform="tidb">

このテーブルを使用して問題のあるステートメントを特定し、クエリのパフォーマンスを向上させる方法については、 [スロークエリログドキュメント](/identify-slow-queries.md)を参照してください。

</CustomContent>

```sql
DESC CLUSTER_SLOW_QUERY;
```

出力は次のとおりです。

```sql
+-------------------------------+---------------------+------+------+---------+-------+
| Field                         | Type                | Null | Key  | Default | Extra |
+-------------------------------+---------------------+------+------+---------+-------+
| INSTANCE                      | varchar(64)         | YES  |      | NULL    |       |
| Time                          | timestamp(6)        | NO   | PRI  | NULL    |       |
| Txn_start_ts                  | bigint(20) unsigned | YES  |      | NULL    |       |
| User                          | varchar(64)         | YES  |      | NULL    |       |
| Host                          | varchar(64)         | YES  |      | NULL    |       |
| Conn_ID                       | bigint(20) unsigned | YES  |      | NULL    |       |
| Exec_retry_count              | bigint(20) unsigned | YES  |      | NULL    |       |
| Exec_retry_time               | double              | YES  |      | NULL    |       |
| Query_time                    | double              | YES  |      | NULL    |       |
| Parse_time                    | double              | YES  |      | NULL    |       |
| Compile_time                  | double              | YES  |      | NULL    |       |
| Rewrite_time                  | double              | YES  |      | NULL    |       |
| Preproc_subqueries            | bigint(20) unsigned | YES  |      | NULL    |       |
| Preproc_subqueries_time       | double              | YES  |      | NULL    |       |
| Optimize_time                 | double              | YES  |      | NULL    |       |
| Wait_TS                       | double              | YES  |      | NULL    |       |
| Prewrite_time                 | double              | YES  |      | NULL    |       |
| Wait_prewrite_binlog_time     | double              | YES  |      | NULL    |       |
| Commit_time                   | double              | YES  |      | NULL    |       |
| Get_commit_ts_time            | double              | YES  |      | NULL    |       |
| Commit_backoff_time           | double              | YES  |      | NULL    |       |
| Backoff_types                 | varchar(64)         | YES  |      | NULL    |       |
| Resolve_lock_time             | double              | YES  |      | NULL    |       |
| Local_latch_wait_time         | double              | YES  |      | NULL    |       |
| Write_keys                    | bigint(22)          | YES  |      | NULL    |       |
| Write_size                    | bigint(22)          | YES  |      | NULL    |       |
| Prewrite_region               | bigint(22)          | YES  |      | NULL    |       |
| Txn_retry                     | bigint(22)          | YES  |      | NULL    |       |
| Cop_time                      | double              | YES  |      | NULL    |       |
| Process_time                  | double              | YES  |      | NULL    |       |
| Wait_time                     | double              | YES  |      | NULL    |       |
| Backoff_time                  | double              | YES  |      | NULL    |       |
| LockKeys_time                 | double              | YES  |      | NULL    |       |
| Request_count                 | bigint(20) unsigned | YES  |      | NULL    |       |
| Total_keys                    | bigint(20) unsigned | YES  |      | NULL    |       |
| Process_keys                  | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_delete_skipped_count  | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_key_skipped_count     | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_cache_hit_count | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_read_count      | bigint(20) unsigned | YES  |      | NULL    |       |
| Rocksdb_block_read_byte       | bigint(20) unsigned | YES  |      | NULL    |       |
| DB                            | varchar(64)         | YES  |      | NULL    |       |
| Index_names                   | varchar(100)        | YES  |      | NULL    |       |
| Is_internal                   | tinyint(1)          | YES  |      | NULL    |       |
| Digest                        | varchar(64)         | YES  |      | NULL    |       |
| Stats                         | varchar(512)        | YES  |      | NULL    |       |
| Cop_proc_avg                  | double              | YES  |      | NULL    |       |
| Cop_proc_p90                  | double              | YES  |      | NULL    |       |
| Cop_proc_max                  | double              | YES  |      | NULL    |       |
| Cop_proc_addr                 | varchar(64)         | YES  |      | NULL    |       |
| Cop_wait_avg                  | double              | YES  |      | NULL    |       |
| Cop_wait_p90                  | double              | YES  |      | NULL    |       |
| Cop_wait_max                  | double              | YES  |      | NULL    |       |
| Cop_wait_addr                 | varchar(64)         | YES  |      | NULL    |       |
| Mem_max                       | bigint(20)          | YES  |      | NULL    |       |
| Disk_max                      | bigint(20)          | YES  |      | NULL    |       |
| KV_total                      | double              | YES  |      | NULL    |       |
| PD_total                      | double              | YES  |      | NULL    |       |
| Backoff_total                 | double              | YES  |      | NULL    |       |
| Write_sql_response_total      | double              | YES  |      | NULL    |       |
| Result_rows                   | bigint(22)          | YES  |      | NULL    |       |
| Warnings                      | longtext            | YES  |      | NULL    |       |
| Backoff_Detail                | varchar(4096)       | YES  |      | NULL    |       |
| Prepared                      | tinyint(1)          | YES  |      | NULL    |       |
| Succ                          | tinyint(1)          | YES  |      | NULL    |       |
| IsExplicitTxn                 | tinyint(1)          | YES  |      | NULL    |       |
| IsWriteCacheTable             | tinyint(1)          | YES  |      | NULL    |       |
| Plan_from_cache               | tinyint(1)          | YES  |      | NULL    |       |
| Plan_from_binding             | tinyint(1)          | YES  |      | NULL    |       |
| Has_more_results              | tinyint(1)          | YES  |      | NULL    |       |
| Plan                          | longtext            | YES  |      | NULL    |       |
| Plan_digest                   | varchar(128)        | YES  |      | NULL    |       |
| Binary_plan                   | longtext            | YES  |      | NULL    |       |
| Prev_stmt                     | longtext            | YES  |      | NULL    |       |
| Query                         | longtext            | YES  |      | NULL    |       |
+-------------------------------+---------------------+------+------+---------+-------+
75 rows in set (0.001 sec)
```

クラスター システム テーブルがクエリされると、TiDB はすべてのノードからデータを取得するのではなく、関連する計算を他のノードにプッシュ ダウンします。実行計画は次のとおりです。

```sql
DESC SELECT COUNT(*) FROM CLUSTER_SLOW_QUERY WHERE user = 'u1';
```

出力は次のとおりです。

```sql
+----------------------------+----------+-----------+--------------------------+------------------------------------------------------+
| id                         | estRows  | task      | access object            | operator info                                        |
+----------------------------+----------+-----------+--------------------------+------------------------------------------------------+
| StreamAgg_7                | 1.00     | root      |                          | funcs:count(1)->Column#75                            |
| └─TableReader_13       | 10.00    | root      |                          | data:Selection_12                                    |
|   └─Selection_12       | 10.00    | cop[tidb] |                          | eq(INFORMATION_SCHEMA.cluster_slow_query.user, "u1") |
|     └─TableFullScan_11 | 10000.00 | cop[tidb] | table:CLUSTER_SLOW_QUERY | keep order:false, stats:pseudo                       |
+----------------------------+----------+-----------+--------------------------+------------------------------------------------------+
4 rows in set (0.00 sec)
```

前述の実行プランでは、条件`user = u1`が他の ( `cop` ) TiDB ノードにプッシュダウンされ、集計演算子もプッシュダウンされます (グラフの`StreamAgg`演算子)。

現在、システム テーブルの統計が収集されないため、一部の集計演算子をプッシュダウンできない場合があり、その結果、実行が遅くなります。この場合、SQL HINT を手動で指定して集計演算子をプッシュダウンできます。例えば：

```sql
SELECT /*+ AGG_TO_COP() */ COUNT(*) FROM CLUSTER_SLOW_QUERY GROUP BY user;
```
