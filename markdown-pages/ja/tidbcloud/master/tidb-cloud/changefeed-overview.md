---
title: Changefeed
summary: TiDB Cloud changefeed helps you stream data from TiDB Cloud to other data services.
---

# チェンジフィード {#changefeed}

TiDB Cloudチェンジフィードは、 TiDB Cloudから他のデータ サービスにデータをストリーミングするのに役立ちます。現在、 TiDB Cloud は、Apache Kafka、MySQL、 TiDB Cloud 、およびクラウドstorageへのストリーミング データをサポートしています。

> **注記：**
>
> -   現在、 TiDB Cloudクラスターあたり最大 100 の変更フィードのみが許可されます。
> -   現在、 TiDB Cloudでは、変更フィードごとに最大 100 個のテーブル フィルター ルールのみが許可されます。
> -   [TiDB サーバーレスクラスター](/tidb-cloud/select-cluster-tier.md#tidb-serverless)の場合、チェンジフィード機能は使用できません。

チェンジフィード機能にアクセスするには、TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[チェンジフィード]**をクリックします。チェンジフィードページが表示されます。

変更フィード ページでは、変更フィードの作成、既存の変更フィードのリストの表示、既存の変更フィードの操作 (変更フィードの拡大縮小、一時停止、再開、編集、削除など) を行うことができます。

## 変更フィードを作成する {#create-a-changefeed}

チェンジフィードを作成するには、次のチュートリアルを参照してください。

-   [Apache Kafka にシンクする](/tidb-cloud/changefeed-sink-to-apache-kafka.md)
-   [MySQL にシンクする](/tidb-cloud/changefeed-sink-to-mysql.md)
-   [TiDB Cloudへのシンク](/tidb-cloud/changefeed-sink-to-tidb-cloud.md)
-   [クラウドstorageにシンクする](/tidb-cloud/changefeed-sink-to-cloud-storage.md)

## 変更フィード RCU のクエリ {#query-changefeed-rcus}

1.  ターゲット TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[Changefeed]**をクリックします。
2.  クエリを実行する対応する変更フィードを見つけて、 **[...]** &gt; **[アクション]**列の**[ビュー]**をクリックします。
3.  現在の TiCDC レプリケーション キャパシティ ユニット (RCU) は、ページの**[仕様]**エリアで確認できます。

## チェンジフィードをスケールする {#scale-a-changefeed}

変更フィードをスケールアップまたはスケールダウンすることによって、変更フィードの TiCDC レプリケーション キャパシティ ユニット (RCU) を変更できます。

> **注記：**
>
> -   クラスターの変更フィードをスケーリングするには、このクラスターのすべての変更フィードが 2023 年 3 月 28 日以降に作成されていることを確認してください。
> -   クラスターに 2023 年 3 月 28 日より前に作成された変更フィードがある場合、このクラスターの既存の変更フィードも新しく作成された変更フィードもスケールアップまたはスケールダウンをサポートしません。

1.  ターゲット TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[Changefeed]**をクリックします。
2.  スケーリングする対応する変更フィードを見つけて、 **[アクション]**列の**[...]** &gt; **[スケールアップ/ダウン]**をクリックします。
3.  新しい仕様を選択します。
4.  **「送信」**をクリックします。

スケーリング プロセスが完了するまでに約 10 分かかり (その間、変更フィードは正常に動作します)、新しい仕様に切り替えるには数秒かかります (その間、変更フィードは一時停止され、自動的に再開されます)。

## チェンジフィードを一時停止または再開する {#pause-or-resume-a-changefeed}

1.  ターゲット TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[Changefeed]**をクリックします。
2.  一時停止または再開する対応する変更フィードを見つけて、 **[アクション]**列の**[...]** &gt; **[一時停止/再開]**をクリックします。

## チェンジフィードを編集する {#edit-a-changefeed}

> **注記：**
>
> TiDB Cloud現在、一時停止ステータスでのみ変更フィードの編集が可能です。

1.  ターゲット TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[Changefeed]**をクリックします。

2.  一時停止する変更フィードを見つけて、 **[アクション]**列の**[...]** &gt; **[一時停止]**をクリックします。

3.  チェンジフィードのステータスが`Paused`に変わったら、 **[...]** &gt; **[編集] を**クリックして、対応するチェンジフィードを編集します。

    TiDB Cloud は、デフォルトで変更フィード構成を設定します。次の構成を変更できます。

    -   Apache Kafka シンク: すべての構成。
    -   MySQL シンク: **MySQL 接続**、**テーブル フィルター**、および**イベント フィルター**。
    -   TiDB Cloudシンク: **TiDB Cloud接続**、**テーブル フィルター**、および**イベント フィルター**。
    -   クラウドstorageシンク:**ストレージ エンドポイント**、**テーブル フィルター**、および**イベント フィルター**。

4.  構成を編集した後、 **[...]** &gt; **[再開]**をクリックして、対応する変更フィードを再開します。

## 変更フィードを削除する {#delete-a-changefeed}

1.  ターゲット TiDB クラスターのクラスター概要ページに移動し、左側のナビゲーション ペインで**[Changefeed]**をクリックします。
2.  削除する対応する変更フィードを見つけて、 **[アクション]**列の**[...]** &gt; **[削除]**をクリックします。

## 変更フィードの請求 {#changefeed-billing}

TiDB Cloudでの変更フィードの請求については、 [変更フィードの請求](/tidb-cloud/tidb-cloud-billing-ticdc-rcu.md)を参照してください。

## フィード状態の変更 {#changefeed-states}

レプリケーション タスクの状態は、レプリケーション タスクの実行状態を表します。プロセスの実行中に、レプリケーション タスクがエラーで失敗したり、手動で一時停止または再開されたり、指定された`TargetTs`に達したりする可能性があります。これらの動作により、レプリケーション タスクの状態が変化する可能性があります。

状態は次のように説明されます。

-   `CREATING` : レプリケーションタスクが作成されています。
-   `RUNNING` : レプリケーション タスクは正常に実行され、チェックポイント ts も正常に進行します。
-   `EDITING` : レプリケーションタスクは編集中です。
-   `PAUSING` : レプリケーションタスクは一時停止中です。
-   `PAUSED` : レプリケーションタスクは一時停止されています。
-   `RESUMING` : レプリケーションタスクが再開されています。
-   `DELETING` : レプリケーションタスクは削除中です。
-   `DELETED` : レプリケーションタスクは削除されます。
-   `WARNING` : レプリケーション タスクは警告を返します。回復可能なエラーがいくつかあるため、レプリケーションを続行できません。この状態のチェンジフィードは、状態が`RUNNING`に移行するまで再開を試み続けます。この状態のチェンジフィードは[GC 操作](https://docs.pingcap.com/tidb/stable/garbage-collection-overview)をブロックします。
-   `FAILED` : レプリケーションタスクは失敗します。何らかのエラーが発生したため、レプリケーション タスクを再開できず、自動的に回復できません。増分データのガベージコレクション(GC) の前に問題が解決された場合は、失敗した変更フィードを手動で再開できます。増分データのデフォルトの Time-To-Live (TTL) 期間は 24 時間です。これは、GC メカニズムは、変更フィードが中断されてから 24 時間以内はデータを削除しないことを意味します。
