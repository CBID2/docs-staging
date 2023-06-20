---
title: TiCDC Overview
summary: Learn what TiCDC is, what features TiCDC provides, and how to install and deploy TiCDC.
---

# TiCDC の概要 {#ticdc-overview}

[TiCDC](https://github.com/pingcap/tiflow/tree/master/cdc)は、TiDB の増分データをレプリケートするために使用されるツールです。具体的には、TiCDC は TiKV 変更ログを取得し、キャプチャしたデータを並べ替えて、行ベースの増分データをダウンストリーム データベースにエクスポートします。

## 使用シナリオ {#usage-scenarios}

-   複数の TiDB クラスターにデータの高可用性と災害復旧ソリューションを提供し、災害発生時にプライマリ クラスターとセカンダリ クラスター間の最終的なデータ整合性を確保します。
-   リアルタイムのデータ変更を同種システムにレプリケートし、モニタリング、キャッシュ、グローバル インデックス作成、データ分析、異種データベース間のプライマリとセカンダリのレプリケーションなどのさまざまなシナリオにデータ ソースを提供します。

## 主な特長 {#major-features}

### 主な機能 {#key-capabilities}

-   第 2 レベルの RPO と分レベルの RTO を使用して、ある TiDB クラスターから別の TiDB クラスターに増分データをレプリケートします。
-   TiDB クラスター間でデータを双方向にレプリケートし、これに基づいて、TiCDC を使用してマルチアクティブ TiDB ソリューションを作成できます。
-   TiDB クラスターから MySQL データベース (または他の MySQL 互換データベース) に低レイテンシーで増分データをレプリケートします。
-   TiDB クラスターから Kafka クラスターに増分データをレプリケートします。推奨するデータ形式には[アブロ](/ticdc/ticdc-avro-protocol.md)があります。
-   データベース、テーブル、DML、および DDL をフィルタリングする機能を備えたテーブルをレプリケートします。
-   単一障害点がなく可用性が高くなります。 TiCDC ノードの動的追加と削除をサポートします。
-   タスク ステータスのクエリ、タスク構成の動的変更、タスクの作成または削除など、 [オープンAPI](/ticdc/ticdc-open-api.md)を通じてクラスター管理をサポートします。

### レプリケーションの順序 {#replication-order}

-   すべての DDL または DML ステートメントについて、TiCDC はそれらを**少なくとも 1 回**出力します。
-   TiKV または TiCDC クラスターで障害が発生すると、TiCDC は同じ DDL/DML ステートメントを繰り返し送信する可能性があります。重複した DDL/DML ステートメントの場合:

    -   MySQL シンクは DDL ステートメントを繰り返し実行できます。 `truncate table`など、ダウンストリームで繰り返し実行できる DDL ステートメントの場合、ステートメントは正常に実行されます。 `create table`など、繰り返し実行できないものについては、実行は失敗し、TiCDC はエラーを無視してレプリケーションを続行します。
    -   カフカシンク
        -   Kafka シンクは、データ分散のためのさまざまな戦略を提供します。テーブル、主キー、またはタイムスタンプに基づいて、データをさまざまな Kafka パーティションに分散できます。これにより、行の更新されたデータが同じパーティションに順番に送信されることが保証されます。
        -   これらすべての配布戦略は、解決された TS メッセージをすべてのトピックとパーティションに定期的に送信します。これは、解決された TS より前のすべてのメッセージがトピックとパーティションに送信されたことを示します。 Kafka コンシューマは、解決された TS を使用して、受信したメッセージを並べ替えることができます。
        -   Kafka シンクは重複したメッセージを送信することがありますが、これらの重複したメッセージは`Resolved Ts`の制約には影響しません。たとえば、チェンジフィードが一時停止されてから再開される場合、Kafka シンクは`msg1` 、 `msg2` 、 `msg3` 、 `msg2` 、および`msg3`順番に送信する可能性があります。 Kafka コンシューマからの重複メッセージをフィルタリングできます。

### レプリケーションの一貫性 {#replication-consistency}

-   MySQLシンク

    -   TiCDC は REDO ログを有効にして、データ レプリケーションの最終的な整合性を確保します。

    -   TiCDC は、単一行の更新の順序がアップストリームの順序と一貫していることを**保証します**。

    -   TiCDC は、ダウンストリーム トランザクションの実行順序がアップストリーム トランザクションの実行順序と同じであることを**保証しません**。

    > **ノート：**
    >
    > v6.2 以降、シンク URI パラメーター[`transaction-atomicity`](/ticdc/ticdc-sink-to-mysql.md#configure-sink-uri-for-mysql-or-tidb)を使用して、単一テーブルのトランザクションを分割するかどうかを制御できます。単一テーブルのトランザクションを分割すると、大規模なトランザクションをレプリケートする際のレイテンシーとメモリ消費量を大幅に削減できます。

## TiCDCアーキテクチャ {#ticdc-architecture}

TiDB の増分データ レプリケーション ツールとして、TiCDC は PD の etcd を通じて高可用性を備えています。レプリケーションのプロセスは次のとおりです。

1.  複数の TiCDC プロセスが TiKV ノードからデータ変更をプルします。
2.  TiKV から取得されたデータ変更は内部でソートおよびマージされます。
3.  データ変更は、複数のレプリケーション タスク (変更フィード) を通じて複数のダウンストリーム システムにレプリケートされます。

TiCDC のアーキテクチャを次の図に示します。

![TiCDC architecture](https://download.pingcap.com/images/docs/ticdc/cdc-architecture.png)

前述のアーキテクチャ図のコンポーネントは次のように説明されています。

-   TiKV サーバー: TiDB クラスター内の TiKV ノード。データが変更されると、TiKV ノードは変更を変更ログ (KV 変更ログ) として TiCDC ノードに送信します。 TiCDC ノードは、変更ログが連続していないと判断した場合、TiKV ノードに変更ログを提供するよう積極的に要求します。
-   TiCDC: TiCDC プロセスが実行される TiCDC ノード。各ノードは TiCDC プロセスを実行します。各プロセスは、TiKV ノード内の 1 つ以上のテーブルからデータ変更を取得し、シンクコンポーネントを介してダウンストリーム システムに変更を複製します。
-   PD: TiDB クラスター内のスケジューリング モジュール。このモジュールはクラスター データのスケジューリングを担当し、通常は 3 つの PD ノードで構成されます。 PD は、etcd クラスターを通じて高可用性を提供します。 etcd クラスターでは、TiCDC はノードのステータス情報や変更フィード構成などのメタデータを保存します。

前述のアーキテクチャ図に示されているように、TiCDC は、TiDB、MySQL、および Kafka データベースへのデータのレプリケーションをサポートしています。

## ベストプラクティス {#best-practices}

-   TiCDC を使用して 2 つの TiDB クラスター間でデータをレプリケートし、クラスター間のネットワークレイテンシーが100 ミリ秒を超える場合は、ダウンストリーム TiDB クラスターが配置されているリージョン (IDC) に TiCDC をデプロイすることをお勧めします。

-   TiCDC は、少なくとも 1 つの**有効なインデックスを**持つテーブルのみを複製します。**有効なインデックスは**次のように定義されます。

    -   主キー ( `PRIMARY KEY` ) は有効なインデックスです。
    -   一意のインデックス ( `UNIQUE INDEX` ) は、インデックスのすべての列が null 非許容として明示的に定義され ( `NOT NULL` )、インデックスに仮想生成列 ( `VIRTUAL GENERATED COLUMNS` ) がない場合に有効です。

-   災害復旧シナリオで TiCDC を使用するには、 [やり直しログ](/ticdc/ticdc-sink-to-mysql.md#eventually-consistent-replication-in-disaster-scenarios)を構成する必要があります。

-   大きな単一行 (1K を超える) を含む幅の広いテーブルをレプリケートする場合は、 `per-table-memory-quota` = `ticdcTotalMemory` /( `tableCount` * 2) となるように[`per-table-memory-quota`](/ticdc/ticdc-server-config.md)を構成することをお勧めします。 `ticdcTotalMemory`は TiCDC ノードのメモリ、 `tableCount`は TiCDC ノードが複製するターゲット テーブルの数です。

> **ノート：**
>
> v4.0.8 以降、TiCDC はタスク構成を変更することで、**有効なインデックスのない**テーブルの複製をサポートします。ただし、これによりデータの一貫性の保証がある程度損なわれます。詳細については、 [有効なインデックスのないテーブルをレプリケートする](/ticdc/ticdc-manage-changefeed.md#replicate-tables-without-a-valid-index)を参照してください。

### サポートされていないシナリオ {#unsupported-scenarios}

現在、次のシナリオはサポートされていません。

-   RawKV のみを使用する TiKV クラスター。
-   TiDB の[シーケンス機能](/sql-statements/sql-statement-create-sequence.md#sequence-function) 。アップストリームの TiDB が`SEQUENCE`使用する場合、TiCDC はアップストリームで実行された`SEQUENCE` DDL 操作/関数を無視します。ただし、 `SEQUENCE`関数を使用する DML 操作は正しく複製できます。

TiCDC は、アップストリームでの大規模なトランザクションのシナリオに対して部分的なサポートのみを提供します。詳細は[TiCDC は大規模なトランザクションのレプリケーションをサポートしていますか?リスクはありますか?](/ticdc/ticdc-faq.md#does-ticdc-support-replicating-large-transactions-is-there-any-risk)を参照してください。