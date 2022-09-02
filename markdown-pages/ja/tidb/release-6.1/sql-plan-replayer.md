---
title: Use PLAN REPLAYER to Save and Restore the On-Site Information of a Cluster
summary: Learn how to use PLAN REPLAYER to save and restore the on-site information of a cluster.
---

# PLAN REPLAYER を使用してクラスタのオンサイト情報を保存および復元する {#use-plan-replayer-to-save-and-restore-the-on-site-information-of-a-cluster}

TiDB クラスターの問題を特定してトラブルシューティングする場合、多くの場合、システムと実行計画に関する情報を提供する必要があります。より便利で効率的な方法で情報を取得し、クラスターの問題をトラブルシューティングするのに役立つように、TiDB v5.3.0 で`PLAN REPLAYER`コマンドが導入されました。このコマンドを使用すると、クラスターのオンサイト情報を簡単に保存および復元でき、トラブルシューティングの効率が向上し、管理のために問題をより簡単にアーカイブできます。

`PLAN REPLAYER`の特徴は以下の通りです。

-   オンサイト トラブルシューティングでの TiDB クラスターの情報を保存用の ZIP 形式のファイルにエクスポートします。
-   別の TiDB クラスターからエクスポートされた ZIP 形式のファイルをクラスターにインポートします。このファイルには、オンサイト トラブルシューティングでの後者の TiDB クラスターの情報が含まれています。

## <code>PLAN REPLAER</code>を使用してクラスター情報をエクスポートする {#use-code-plan-replaer-code-to-export-cluster-information}

`PLAN REPLAYER`を使用して、TiDB クラスターのオンサイト情報を保存できます。エクスポート インターフェイスは次のとおりです。


```sql
PLAN REPLAYER DUMP EXPLAIN [ANALYZE] sql-statement;
```

`sql-statement`に基づいて、TiDB は次のオンサイト情報を整理してエクスポートします。

-   TiDB バージョン
-   TiDB 構成
-   TiDB セッション変数
-   TiDB SQLバインディング
-   `sql-statement`のテーブル スキーマ
-   `sql-statement`の表の統計
-   `EXPLAIN [ANALYZE] sql-statement`の結果

> **ノート：**
>
> `PLAN REPLAYER`はテーブル データをエクスポートし**ません**。

### クラスター情報のエクスポートの例 {#examples-of-exporting-cluster-information}


```sql
use test;
create table t(a int, b int);
insert into t values(1,1), (2, 2), (3, 3);
analyze table t;

plan replayer dump explain select * from t;
```

`PLAN REPLAYER DUMP`は、上記のテーブル情報を`ZIP`ファイルにパッケージ化し、ファイル識別子を実行結果として返します。このファイルは 1 回限りのファイルです。ファイルがダウンロードされると、TiDB はそのファイルを削除します。

> **ノート：**
>
> `ZIP`のファイルは、最大 1 時間 TiDB クラスターに保存されます。 1 時間後、TiDB はそれを削除します。

```sql
MySQL [test]> plan replayer dump explain select * from t;
+------------------------------------------------------------------+
| Dump_link                                                        |
+------------------------------------------------------------------+
| replayer_single_JOGvpu4t7dssySqJfTtS4A==_1635750890568691080.zip |
+------------------------------------------------------------------+
1 row in set (0.015 sec)
```

ファイルは MySQL クライアントにダウンロードできないため、TiDB HTTP インターフェイスとファイル識別子を使用してファイルをダウンロードする必要があります。


```shell
http://${tidb-server-ip}:${tidb-server-status-port}/plan_replayer/dump/${file_token}
```

`${tidb-server-ip}:${tidb-server-status-port}`は、クラスター内の任意の TiDBサーバーのアドレスです。例えば：


```shell
curl http://127.0.0.1:10080/plan_replayer/dump/replayer_single_JOGvpu4t7dssySqJfTtS4A==_1635750890568691080.zip > plan_replayer.zip
```

## <code>PLAN REPLAYER</code>を使用してクラスター情報をインポートする {#use-code-plan-replayer-code-to-import-cluster-information}

> **警告：**
>
> TiDB クラスターのオンサイト情報を別のクラスターにインポートすると、後者のクラスターの TiDB セッション変数、SQL バインディング、テーブル スキーマ、および統計が変更されます。

`PLAN REPLAYER`を使用してエクスポートされた既存の`ZIP`ファイルを使用して、 `PLAN REPLAYER`インポート インターフェイスを使用して、クラスターのオンサイト情報を他の TiDB クラスターに復元できます。構文は次のとおりです。


```sql
PLAN REPLAYER LOAD 'file_name';
```

上記のステートメントで、 `file_name`はエクスポートする`ZIP`ファイルの名前です。

例えば：


```sql
PLAN REPLAYER LOAD 'plan_replayer.zip';
```
