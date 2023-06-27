---
title: PD Scheduling Best Practices
summary: Learn best practice and strategy for PD scheduling.
---

# PD スケジュールのベスト プラクティス {#pd-scheduling-best-practices}

このドキュメントでは、アプリケーションを容易にするための一般的なシナリオを通じて PD スケジューリングの原則と戦略について詳しく説明します。このドキュメントは、次の中心的な概念を含む TiDB、TiKV、および PD の基本を理解していることを前提としています。

-   [リーダー/フォロワー/学習者](/glossary.md#leaderfollowerlearner)
-   [オペレーター](/glossary.md#operator)
-   [オペレータステップ](/glossary.md#operator-step)
-   [保留中/ダウン中](/glossary.md#pendingdown)
-   [リージョン/ピア/ Raftグループ](/glossary.md#regionpeerraft-group)
-   [リージョン分割](/glossary.md#region-split)
-   [スケジューラ](/glossary.md#scheduler)
-   [店](/glossary.md#store)

> **ノート：**
>
> このドキュメントは当初、TiDB 3.0 を対象としています。一部の機能は以前のバージョン (2.x) ではサポートされていませんが、基礎となるメカニズムは類似しているため、このドキュメントは引き続き参照として使用できます。

## PD スケジューリング ポリシー {#pd-scheduling-policies}

このセクションでは、スケジューリング システムに含まれる原理とプロセスを紹介します。

### スケジューリングプロセス {#scheduling-process}

スケジューリング プロセスには通常、次の 3 つのステップがあります。

1.  情報を収集する

    各 TiKV ノードは、次の 2 種類のハートビートを PD に定期的に報告します。

    -   `StoreHeartbeat` : ディスク容量、利用可能なstorage、読み取り/書き込みトラフィックなど、ストアの全体的な情報が含まれます。
    -   `RegionHeartbeat` : 各リージョンの範囲、ピアの分布、ピアのステータス、データ量、読み取り/書き込みトラフィックなど、リージョンの全体的な情報が含まれます。

    PD は、スケジューリング決定のためにこの情報を収集および復元します。

2.  演算子の生成

    さまざまなスケジューラは、次の点を考慮して、独自のロジックと要件に基づいて演算子を生成します。

    -   異常な状態 (切断、ダウン、ビジー、スペース不足) のストアにピアを追加しないでください。
    -   異常な状態の領域のバランスをとらないでください
    -   リーダーを保留中のピアに転送しないでください
    -   リーダーを直接削除しないでください
    -   さまざまなリージョン ピアの物理的分離を壊さないでください
    -   ラベルプロパティなどの制約に違反しないでください

3.  演算子の実行

    演算子を実行する一般的な手順は次のとおりです。

    1.  生成されたオペレーターは、まず`OperatorController`によって管理されるキューに参加します。

    2.  `OperatorController` 、オペレーターをキューから取り出し、構成に基づいて一定量の同時実行性でオペレーターを実行します。このステップでは、各オペレータ ステップを対応する領域リーダーに割り当てます。

    3.  オペレーターは「終了」または「タイムアウト」としてマークされ、キューから削除されます。

### 負荷分散 {#load-balancing}

リージョンは主に`balance-leader`と`balance-region`スケジューラに依存して負荷分散を実現します。どちらのスケジューラーも、クラスター内のすべてのストアにリージョンを均等に分散することをターゲットとしていますが、焦点は別々です`balance-leader`受信クライアント要求のバランスを取るためにリージョン リーダーを扱いますが、 `balance-region`はstorageの圧力を再分散し、storageスペース不足などの例外を回避するために各リージョン ピアに関係します。 。

`balance-leader`と`balance-region`同様のスケジューリング プロセスを共有します。

1.  リソースの可用性に応じてストアを評価します。
2.  `balance-leader`または`balance-region`リーダーまたはピアをスコアの高い店舗からスコアの低い店舗に常に移動させます。

ただし、評価方法は異なります。 `balance-leader`ストア内のリーダーに対応するすべての領域サイズの合計を使用しますが、 `balance-region`の方法は比較的複雑です。各ノードの特定のstorage容量に応じて、評価方法`balance-region`は次のようになります。

-   十分なstorageがある場合のデータ量に基づいて (ノード間のデータ分散のバランスを取るため)。
-   storageに基づいて (異なるノード上のstorageの可用性のバランスを取るため)。
-   どちらの状況にも当てはまらない場合は、上記の 2 つの要素の加重合計に基づきます。

ノードが異なればパフォーマンスが異なる可能性があるため、ストアごとに負荷分散の重みを設定することもできます。 `leader-weight`と`region-weight` 、それぞれリーダーの重みと領域の重みを制御するために使用されます (デフォルトでは両方とも「1」)。例えば、ある店舗の`leader-weight` 「２」に設定すると、スケジューリングが安定した後のノードのリーダー数は他のノードの約２倍となる。同様に、店舗の`leader-weight` 「0.5」に設定すると、そのノードのリーダーの数は他のノードの約半分になります。

### ホットリージョンのスケジュール設定 {#hot-regions-scheduling}

ホット領域のスケジュールには`hot-region-scheduler`を使用します。 TiDB v3.0 以降、プロセスは次のように実行されます。

1.  ストアから報告された情報に基づいて、一定期間に一定のしきい値を超える読み取り/書き込みトラフィックを判断し、ホット リージョンをカウントします。

2.  負荷分散と同様の方法でこれらのリージョンを再分散します。

ホット書き込みリージョンの場合、 `hot-region-scheduler`はリージョン ピアとリーダーの両方を再配布しようとします。ホット読み取りリージョンの場合、 `hot-region-scheduler`リージョン リーダーのみを再配布します。

### クラスタトポロジーの認識 {#cluster-topology-awareness}

クラスタトポロジの認識により、PD はリージョンのレプリカを可能な限り分散できるようになります。これにより、TiKV は高可用性と災害復旧機能を確保します。 PD はバックグラウンドですべての領域を継続的にスキャンします。 PD は、領域の分散が最適ではないことを検出すると、ピアを置き換えて領域を再分散するためのオペレーターを生成します。

領域の分布を確認するコンポーネントは`replicaChecker`です。これは、無効にできない点を除けばスケジューラと似ています。 `location-labels`の構成に基づく`replicaChecker`スケジュール。たとえば、 `[zone,rack,host]`クラスターの 3 層トポロジを定義します。 PD は、最初に異なるゾーンにリージョン ピアをスケジュールするか、ゾーンが不十分な場合 (たとえば、3 つのレプリカに対して 2 つのゾーン) に別のラックに、またはラックが不十分な場合に別のホストにリージョン ピアをスケジュールしようとします。

### スケールダウンと障害復旧 {#scale-down-and-failure-recovery}

スケールダウンとは、コマンドを使用してストアをオフラインにし、「オフライン」としてマークするときのプロセスを指します。 PD は、スケジュールによってオフライン ノード上の領域を他のノードに複製します。障害回復は、ストアに障害が発生し回復できない場合に適用されます。この場合、対応するストアにピアが分散されているリージョンではレプリカが失われる可能性があり、PD が他のノードで補充する必要があります。

スケールダウンと障害復旧のプロセスは基本的に同じです。 `replicaChecker`異常な状態にあるリージョン ピアを検出し、異常なピアを正常なストア上の新しいピアに置き換えるオペレーターを生成します。

### リージョンのマージ {#region-merge}

リージョンのマージとは、隣接する小さな領域をマージするプロセスを指します。これは、データ削除後の多数の小さな領域または空の領域による不必要なリソースの消費を回避するのに役立ちます。リージョンのマージは`mergeChecker`によって実行されます。これは`replicaChecker`と同様の方法で処理されます。PD はバックグラウンドですべての領域を継続的にスキャンし、連続した小さな領域が見つかった場合にオペレーターを生成します。

具体的には、新しく分割されたリージョンが値[`split-merge-interval`](/pd-configuration-file.md#split-merge-interval) (デフォルトでは`1h` ) を超えて存在する場合、次の条件が同時に発生すると、このリージョンはリージョンのマージ スケジューリングをトリガーします。

-   このリージョンのサイズは[`max-merge-region-size`](/pd-configuration-file.md#max-merge-region-size)の値より小さいです (デフォルトでは 20 MiB)。

-   このリージョンのキーの数は、値[`max-merge-region-keys`](/pd-configuration-file.md#max-merge-region-keys) (デフォルトでは 200,000) より小さいです。

## スケジュールステータスのクエリ {#query-scheduling-status}

スケジューリングシステムの状態はメトリクス、pd-ctl、ログで確認できます。ここではメトリクスとpd-ctlの手法について簡単に紹介します。詳細は[PD Control](/pd-control.md)を参照してください。

### オペレーターのステータス {#operator-status}

**Grafana PD/オペレーター**ページには、オペレーターに関するメトリックが表示されます。その中には次のものがあります。

-   スケジュールオペレータ作成：オペレータ作成情報
-   オペレーターの終了時間: 各オペレーターが費やした実行時間
-   オペレータ ステップの継続時間: オペレータ ステップによって消費された実行時間

次のコマンドで pd-ctl を使用して演算子をクエリできます。

-   `operator show` : 現在のスケジューリング タスクで生成されたすべての演算子をクエリします。
-   `operator show [admin | leader | region]` : タイプ別に演算子をクエリします

### 残高ステータス {#balance-status}

**Grafana PD/統計- バランス**ページには、ロード バランシングに関するメトリクスが表示されます。その中には次のようなものがあります。

-   ストアリーダー/地域スコア: 各ストアのスコア
-   ストア リーダー/リージョン数: 各ストアのリーダー/リージョンの数
-   利用可能なストア: 各ストアで利用可能なstorage

pd-ctlのstoreコマンドを使用して、各ストアの残高状況を問い合わせることができます。

### ホットリージョンのステータス {#hot-region-status}

**Grafana PD/統計- ホットスポット**ページには、次のようなホット リージョンに関するメトリクスが表示されます。

-   ホット書き込み領域のリーダー/ピアの分布: ホット書き込み領域のリーダー/ピアの分布
-   ホット読み取り領域のリーダー分布: ホット読み取り領域のリーダー分布

次のコマンドで pd-ctl を使用してホット リージョンのステータスをクエリすることもできます。

-   `hot read` : ホットリード領域をクエリします。
-   `hot write` : ホットライト領域をクエリします
-   `hot store` : 店舗ごとのホット リージョンの分布を問い合わせます
-   `region topread [limit]` : 読み取りトラフィックが最も多い領域をクエリします。
-   `region topwrite [limit]` : 書き込みトラフィックが最も多い領域をクエリします。

### リージョンの健康 {#region-health}

**Grafana PD/クラスタ/リージョン健全性**パネルには、異常な状態にあるリージョンに関するメトリクスが表示されます。

pd-ctl と領域チェック コマンドを使用して、異常な状態にある領域のリストをクエリできます。

-   `region check miss-peer` : 十分なピアがないリージョンをクエリします。
-   `region check extra-peer` : 追加のピアを持つ領域をクエリします
-   `region check down-peer` : ピアがダウンしている領域をクエリします
-   `region check pending-peer` : 保留中のピアのある領域をクエリします。

## スケジュール戦略を制御する {#control-scheduling-strategy}

pd-ctl を使用すると、次の 3 つの側面からスケジューリング戦略を調整できます。詳細については[PD Control](/pd-control.md)を参照してください。

### スケジューラを手動で追加/削除する {#add-delete-scheduler-manually}

PD は、pd-ctl を介して直接スケジューラを動的に追加および削除することをサポートしています。例えば：

-   `scheduler show` : システム内で現在実行中のスケジューラーを表示します。
-   `scheduler remove balance-leader-scheduler` : バランス リーダー スケジューラを削除 (無効)
-   `scheduler add evict-leader-scheduler 1` : ストア 1 のすべてのリーダーを削除するスケジューラを追加します。

### オペレータを手動で追加/削除する {#add-delete-operators-manually}

PD は、pd-ctl を介して直接演算子の追加または削除もサポートします。例えば：

-   `operator add add-peer 2 5` : ストア 5 のリージョン2 にピアを追加します
-   `operator add transfer-leader 2 5` :リージョン2 のリーダーをストア 5 に移行します
-   `operator add split-region 2` :リージョン2 を均等なサイズの 2 つの領域に分割します。
-   `operator remove 2` :リージョン2 で現在保留中のオペレータを削除します。

### スケジュールパラメータを調整する {#adjust-scheduling-parameter}

pd-ctl の`config show`コマンドを使用してスケジューリング構成を確認し、 `config set {key} {value}`使用して値を調整できます。一般的な調整には次のようなものがあります。

-   `leader-schedule-limit` : 転送リーダーのスケジューリングの同時実行性を制御します。
-   `region-schedule-limit` : ピアスケジューリングの追加/削除の同時実行性を制御します。
-   `enable-replace-offline-replica` : ノードをオフラインにするスケジューリングを有効にするかどうかを決定します。
-   `enable-location-replacement` : 領域の分離レベルを処理するスケジューリングを有効にするかどうかを決定します。
-   `max-snapshot-count` : 各ストアのスナップショットの送信/受信の最大同時実行数を制御します。

## 一般的なシナリオでの PD スケジューリング {#pd-scheduling-in-common-scenarios}

このセクションでは、いくつかの典型的なシナリオを通じて PD スケジューリング戦略のベスト プラクティスを説明します。

### リーダー/地域が均等に分散されていない {#leaders-regions-are-not-evenly-distributed}

PD の評価メカニズムでは、さまざまなストアのリーダー数とリージョン数が負荷分散ステータスを完全に反映できないと判断されます。したがって、TiKV の実際の負荷やstorageの使用状況から負荷の不均衡が発生していないかを確認する必要があります。

リーダー/リージョンが均等に分布していないことを確認したら、さまざまなストアの評価を確認する必要があります。

さまざまな店舗のスコアが近い場合、PD はリーダー/地域が均等に分布していると誤って信じていることを意味します。考えられる理由は次のとおりです。

-   負荷の不均衡を引き起こすホットな領域があります。この場合、 [ホットリージョンのスケジュール設定](#hot-regions-are-not-evenly-distributed)に基づいてさらに分析する必要があります。
-   空き領域または小さな領域が多数存在するため、店舗ごとのリーダーの数に大きな差が生じ、 Raft店舗へのプレッシャーが高くなります。これは[リージョンのマージ](#region-merge-is-slow)スケジュールの時間です。
-   ハードウェアおよびソフトウェア環境は店舗によって異なります。リーダー/リージョンの分布を制御するには、 [負荷分散](#load-balancing)を参照し、 `leader-weight`と`region-weight`の値を調整します。
-   その他の不明な理由。それでも、 `leader-weight`と`region-weight`の値を調整して、リーダー/領域の分布を制御できます。

異なるストアの評価に大きな違いがある場合は、オペレーターの生成と実行に特に焦点を当てて、オペレーター関連のメトリクスを調べる必要があります。主に次の 2 つの状況があります。

-   オペレーターは正常に生成されるが、スケジューリング プロセスが遅い場合は、次の可能性があります。

    -   スケジューリング速度は、負荷分散の目的でデフォルトで制限されています。通常のサービスに大きな影響を与えることなく、 `leader-schedule-limit`または`region-schedule-limit`より大きな値に調整できます。また、 `max-pending-peer-count` 、 `max-snapshot-count`の制限も適切に緩和することができます。
    -   他のスケジュール設定タスクが同時に実行されているため、バランスが遅くなります。この場合、バランス調整が他のスケジュール設定タスクよりも優先される場合は、他のタスクを停止したり、速度を制限したりできます。たとえば、バランシングの進行中に一部のノードをオフラインにすると、両方の操作で`region-schedule-limit`のクォータが消費されます。この場合、スケジューラの速度を制限してノードを削除するか、単純に`enable-replace-offline-replica = false`を設定して一時的に無効にすることができます。
    -   スケジューリングプロセスが遅すぎます。**オペレーター ステップ期間**メトリックを確認して、原因を確認できます。一般に、スナップショットの送受信を含まないステップ ( `TransferLeader` 、 `RemovePeer` 、 `PromoteLearner`など) はミリ秒で完了する必要がありますが、スナップショットを含むステップ ( `AddLearner`や`AddPeer`など) は数十秒で完了することが予想されます。時間が明らかに長すぎる場合は、TiKV への高い負荷またはネットワークのボトルネックが原因である可能性があるため、特別な分析が必要です。

-   PD は、対応するバランシング スケジューラの生成に失敗します。考えられる理由は次のとおりです。

    -   スケジューラが起動されていません。たとえば、対応するスケジューラが削除されるか、その制限が「0」に設定されます。
    -   その他の制約。たとえば、システム内の`evict-leader-scheduler`リーダーが対応するストアに移行できないようにします。または、ラベル プロパティが設定されているため、一部の店舗がリーダーを拒否します。
    -   クラスタトポロジによる制限。たとえば、3 つのデータ センターにまたがる 3 つのレプリカからなるクラスターでは、レプリカの分離により、各リージョンの 3 つのレプリカが異なるデータ センターに分散されます。これらのデータ センター間で店舗の数が異なる場合、スケジューリングは各データ センター内でのみバランスの取れた状態に到達できますが、グローバルにバランスが取れなくなります。

### ノードをオフラインにするのが遅い {#taking-nodes-offline-is-slow}

このシナリオでは、関連するメトリックを通じてオペレーターの生成と実行を調べる必要があります。

オペレーターは正常に生成されたものの、スケジューリング プロセスが遅い場合は、次の理由が考えられます。

-   スケジュール速度はデフォルトで制限されています。 `leader-schedule-limit`または`replica-schedule-limit`をより大きな値に調整できます。s 同様に、 `max-pending-peer-count`と`max-snapshot-count`の制限を緩めることを検討できます。
-   他のスケジューリング タスクが同時に実行され、システム内のリソースを奪い合っています。 [リーダー/地域が均等に分散されていない](#leadersregions-are-not-evenly-distributed)の解決策を参照してください。
-   単一のノードをオフラインにすると、処理されるリージョン リーダーの数 (3 つのレプリカ構成では約 1/3) が削除するノードに分散されます。したがって、速度は、この単一ノードによってスナップショットが生成される速度によって制限されます。リーダーを移行するために手動で`evict-leader-scheduler`を追加すると、速度を上げることができます。

対応する演算子の生成に失敗した場合、考えられる理由は次のとおりです。

-   オペレータが停止するか、 `replica-schedule-limit`が「0」に設定されます。
-   リージョン移行に適切なノードがありません。たとえば、(同じラベルの) 交換ノードの利用可能な容量サイズが 20% 未満の場合、PD はそのノード上のstorageの不足を避けるためにスケジューリングを停止します。このような場合、ノードを追加するか、一部のデータを削除してスペースを解放する必要があります。

### ノードをオンラインにするのが遅い {#bringing-nodes-online-is-slow}

現在、ノードをオンラインにすることは、バランス リージョン メカニズムを通じてスケジュールされています。トラブルシューティングについては[リーダー/地域が均等に分散されていない](#leadersregions-are-not-evenly-distributed)を参照してください。

### 高温領域が均等に分布していない {#hot-regions-are-not-evenly-distributed}

ホット リージョンのスケジュールの問題は、通常、次のカテゴリに分類されます。

-   ホット リージョンは PD メトリクスを介して観察できますが、スケジューリング速度が追いつかず、ホット リージョンを時間内に再配布できません。

    **解決策**: `hot-region-schedule-limit`をより大きな値に調整し、他のスケジューラーの制限クォータを減らして、ホット リージョンのスケジューリングを高速化します。または、 `hot-region-cache-hits-threshold`をより小さい値に調整して、PD がトラフィックの変化に対してより敏感になるようにすることもできます。

-   単一の領域上に形成されたホットスポット。たとえば、小さなテーブルが大量のリクエストによって集中的にスキャンされます。これは PD メトリクスからも検出できます。実際には 1 つのホットスポットを分散することはできないため、そのような領域を分割するには`split-region`オペレーターを手動で追加する必要があります。

-   TiKV 関連のメトリクスから、一部のノードの負荷が他のノードの負荷よりも大幅に高く、これがシステム全体のボトルネックになります。現在、PD はトラフィック分析のみを通じてホットスポットをカウントしているため、特定のシナリオでは PD がホットスポットを特定できない可能性があります。たとえば、一部のリージョンに対して集中的なポイント ルックアップ リクエストがある場合、トラフィック内での検出が明らかではない場合がありますが、それでも高い QPS が主要モジュールでボトルネックを引き起こす可能性があります。

    **解決策**: まず、特定のビジネスに基づいてホット リージョンが形成されているテーブルを見つけます。次に、 `scatter-range-scheduler`スケジューラを追加して、このテーブルのすべての領域が均等に分散されるようにします。 TiDB は、この操作を簡素化するための HTTP API インターフェースも提供します。詳細は[TiDB HTTP API](https://github.com/pingcap/tidb/blob/master/docs/tidb_http_api.md)を参照してください。

### リージョンのマージが遅い {#region-merge-is-slow}

遅いスケジューリングと同様に、領域マージの速度は`merge-schedule-limit`と`region-schedule-limit`の構成によって制限されているか、領域マージ スケジューラが他のスケジューラと競合している可能性があります。具体的には、次のような解決策があります。

-   システム内に多数の空の領域があることがメトリクスからわかっている場合は、 `max-merge-region-size`と`max-merge-region-keys`をより小さい値に調整して、マージを高速化できます。これは、マージ プロセスにはレプリカの移行が含まれるため、マージする領域が小さいほどマージが速くなります。マージ演算子がすでに迅速に生成されている場合は、プロセスをさらに高速化するために、 `patrol-region-interval`から`10ms`に設定できます (TiDB の v5.3.0 以降のバージョンでは、この構成項目のデフォルト値は`10ms`です)。これにより、CPU 消費量が増加しますが、領域スキャンが高速になります。

-   多くのテーブルが作成されてから空になりました (切り捨てられたテーブルを含む)。分割テーブル属性が有効な場合、これらの空のリージョンをマージすることはできません。次のパラメータを調整することで、この属性を無効にできます。

    -   TiKV: `split-region-on-table` ～ `false`を設定します。パラメータを動的に変更することはできません。
    -   PD: PD Controlを使用して、クラスターの状況に必要なパラメーターを設定します。

        -   クラスターに TiDB インスタンスがなく、値[`key-type`](/pd-control.md#config-show--set-option-value--placement-rules)が`raw`または`txn`に設定されているとします。この場合、PD は`enable-cross-table-merge setting`の値に関係なく、テーブル全体でリージョンをマージできます。 `key-type`パラメータは動的に変更できます。

        
        ```bash
        config set key-type txn
        ```

        -   クラスターに TiDB インスタンスがあり、値`key-type`が`table`に設定されているとします。この場合、PD は、 `enable-cross-table-merge`の値が`true`に設定されている場合にのみ、テーブル間でリージョンをマージできます。 `key-type`パラメータは動的に変更できます。

        
        ```bash
        config set enable-cross-table-merge true
        ```

        変更が反映されない場合は、 [FAQ - TiKV/PD 用に変更した`toml`構成が有効にならないのはなぜですか?](/faq/deploy-and-maintain-faq.md#why-the-modified-toml-configuration-for-tikvpd-does-not-take-effect)を参照してください。

        > **ノート：**
        >
        > 配置ルールを有効にした後、デコードの失敗を避けるために、値`key-type`適切に切り替えてください。

v3.0.4 および v2.1.16 以前の場合、特定の状況では領域の`approximate_keys`が不正確になり (そのほとんどはテーブルの削除後に発生します)、そのためキーの数が`max-merge-region-keys`の制約を破ります。この問題を回避するには、 `max-merge-region-keys`をより大きな値に調整します。

### TiKV ノードのトラブルシューティング {#troubleshoot-tikv-node}

TiKV ノードに障害が発生した場合、PD はデフォルトで 30 分後に対応するノードを**ダウン**状態に設定し (構成項目`max-store-down-time`でカスタマイズ可能)、関係するリージョンのレプリカのバランスを再調整します。

実際には、ノード障害が回復不可能とみなされる場合は、すぐにオフラインにすることができます。これにより、PD はすぐに別のノードにレプリカを補充し、データ損失のリスクを軽減します。対照的に、ノードが回復可能であると考えられているものの、回復を 30 分以内に実行できない場合は、タイムアウト後の不必要なレプリカの補充とリソースの浪費を避けるために、一時的に`max-store-down-time`をより大きな値に調整できます。

TiDB v5.2.0 では、TiKV に低速な TiKV ノード検出のメカニズムが導入されています。 TiKV でリクエストをサンプリングすることにより、このメカニズムは 1 ～ 100 の範囲のスコアを算出します。スコアが 80 以上の TiKV ノードは低速としてマークされます。 [`evict-slow-store-scheduler`](/pd-control.md#scheduler-show--add--remove--pause--resume--config--describe)を追加すると、遅いノードを検出してスケジュールすることができます。 TiKV が 1 つだけ低速として検出され、低速スコアが上限 (デフォルトでは 100) に達した場合、このノードのリーダーが排除されます ( `evict-leader-scheduler`の効果と同様)。