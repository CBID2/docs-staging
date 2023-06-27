---
title: TiDB 2.0 Release Notes
---

# TiDB 2.0 リリースノート {#tidb-2-0-release-notes}

2018 年 4 月 27 日に、TiDB 2.0 GA がリリースされました。 TiDB 1.0 と比較して、このリリースでは MySQL の互換性、SQL オプティマイザー、エグゼキューター、安定性が大幅に向上しています。

## TiDB {#tidb}

-   SQLオプティマイザー
    -   よりコンパクトなデータ構造を使用して、統計情報のメモリ使用量を削減します。
    -   tidb-server プロセスの開始時に統計情報のロードを高速化します。
    -   統計情報の動的更新をサポート [実験的]
    -   コスト モデルを最適化して、より正確なクエリ コスト評価を提供します。
    -   ポイント クエリのコストをより正確に見積もるには`Count-Min Sketch`を使用します。
    -   インデックスを最大限に活用するために、より複雑な条件の分析をサポートします
    -   `STRAIGHT_JOIN`構文を使用した`Join`注文の手動指定のサポート
    -   `GROUP BY`句が空の場合は、パフォーマンスを向上させるために Stream 集計演算子を使用します。
    -   `MAX/MIN`関数のインデックスの使用をサポート
    -   相関サブクエリの処理アルゴリズムを最適化して、より多くの種類の相関サブクエリの非相関化をサポートし、それらを`Left Outer Join`つに変換します。
    -   インデックス接頭辞の照合に使用される拡張`IndexLookupJoin`
-   SQL実行エンジン
    -   Chunkアーキテクチャを使用してすべての演算子をリファクタリングし、分析クエリの実行パフォーマンスを向上させ、メモリ使用量を削減します。 TPC-H ベンチマーク結果には大幅な改善が見られます。
    -   ストリーミング集計演算子のプッシュダウンをサポートする
    -   `Insert Into Ignore`ステートメントを最適化してパフォーマンスを 10 倍以上向上させる
    -   `Insert On Duplicate Key Update`ステートメントを最適化してパフォーマンスを 10 倍以上向上させる
    -   `Load Data`最適化するとパフォーマンスが 10 倍以上向上します
    -   より多くのデータ型と関数をTiKV にプッシュダウンする
    -   物理オペレーターのメモリ使用量の計算、およびメモリ使用量がしきい値を超えた場合の構成ファイルおよびシステム変数での処理動作の指定をサポートします。
    -   OOM のリスクを軽減するために、単一の SQL ステートメントによるメモリ使用量の制限をサポートします。
    -   CRUD 操作での暗黙的な RowID の使用のサポート
    -   ポイントクエリのパフォーマンスを向上させる
-   サーバ
    -   プロキシプロトコルのサポート
    -   監視メトリクスをさらに追加し、ログを改良する
    -   構成ファイルの検証のサポート
    -   HTTP APIを介したTiDBパラメータ情報の取得をサポート
    -   バッチ モードでロックを解決してガベージコレクションを高速化します
    -   マルチスレッドのガベージコレクションをサポート
    -   TLSのサポート
-   互換性
    -   より多くの MySQL 構文をサポートする
    -   OGG データ レプリケーション ツールをサポートするための構成ファイル内の`lower_case_table_names`システム変数の変更のサポート
    -   Navicat 管理ツールとの互換性の向上
    -   `Information_Schema`でのテーブル作成時間の表示をサポート
    -   一部の関数/式の戻り値の型が MySQL と異なる問題を修正
    -   JDBCとの互換性の向上
    -   より多くの SQL モードをサポート
-   DDL
    -   `Add Index`オペレーションを最適化して、一部のシナリオで実行速度を大幅に向上させます。
    -   オンライン ビジネスへの影響を軽減するために、 `Add Index`操作の優先順位を低くします。
    -   `Admin Show DDL Jobs`のDDLジョブのより詳細なステータス情報を出力します。
    -   `Admin Show DDL Job Queries JobID`を使用した、現在実行中の DDL ジョブの元のステートメントのクエリのサポート
    -   ディザスタリカバリのために`Admin Recover Index`を使用したインデックスデータのリカバリをサポート
    -   `Alter`ステートメントを使用したテーブル オプションの変更のサポート

## PD {#pd}

-   サポート`Region Merge` 、データ削除後に空のリージョンをマージする [実験的]
-   サポート`Raft Learner` [実験的]
-   スケジューラーを最適化する
    -   スケジューラをさまざまなリージョンサイズに適応させるようにする
    -   TiKV 停止時のデータ復元の優先度と速度を向上します。
    -   TiKV ノードを削除する際のデータ転送を高速化します。
    -   TiKV ノードのスペースが不十分な場合にディスクがいっぱいになるのを防ぐために、スケジューリング ポリシーを最適化します。
    -   バランスリーダースケジューラのスケジューリング効率を向上させます。
    -   バランス領域スケジューラのスケジューリング オーバーヘッドを削減します。
    -   ホットリージョンスケジューラの実行効率を最適化します。
-   操作インターフェイスと構成
    -   TLSのサポート
    -   PDリーダーを優先してサポート
    -   ラベルに基づいたスケジューリング ポリシーの構成のサポート
    -   Raftリーダーをスケジュールしないように特定のラベルを持つストアの構成をサポートします
    -   単一リージョン内のホットスポットを処理するためのリージョンの手動分割をサポートします。
    -   場合によっては、指定したリージョンの分散をサポートして、リージョンの分布を手動で調整することができます。
    -   設定パラメータのチェックルールを追加し、設定項目の有効性チェックを強化します
-   デバッグインターフェース
    -   `Drop Region`デバッグ インターフェイスを追加します
    -   各 PD の健全性ステータスを列挙するインターフェイスを追加します。
-   統計
    -   異常な領域に関する統計を追加します
    -   リージョン分離レベルに関する統計を追加します
    -   スケジュール関連の指標を追加する
-   パフォーマンス
    -   書き込みパフォーマンスを向上させるために、PD リーダーと etcd リーダーを同じノード内にまとめて維持します
    -   リージョンハートビートのパフォーマンスを最適化する

## TiKV {#tikv}

-   特徴
    -   重要な構成を誤った変更から保護する
    -   サポート`Region Merge` [実験的]
    -   `Raw DeleteRange` APIを追加
    -   `GetMetric` APIを追加
    -   `Raw Batch Put` 、 `Raw Batch Get` 、 `Raw Batch Delete` 、 `Raw Batch Scan`を加算します
    -   RawKV API のカラムファミリー オプションを追加し、特定のカラムファミリーでの操作の実行をサポートします
    -   コプロセッサーでのストリーミングおよびストリーミング集計のサポート
    -   コプロセッサーのリクエストタイムアウトの設定をサポート
    -   リージョンのハートビートでタイムスタンプを伝達する
    -   一部の RocksDB パラメーターのオンライン変更をサポート ( `block-cache-size`など)
    -   警告またはエラーが発生した場合のコプロセッサーの動作の構成をサポートします。
    -   データインポートプロセス中の書き込み増幅を軽減するために、データインポートモードでの開始をサポートします。
    -   手動によるリージョンの半分への分割をサポート
    -   データ回復ツールの改善`tikv-ctl`
    -   TiDB の動作をガイドするために、コプロセッサーでより多くの統計を返します。
    -   SST ファイルをインポートする`ImportSST` API をサポート [実験的]
    -   TiKV Importer バイナリを追加してTiDB Lightningと統合し、データを迅速にインポートします [実験的]
-   パフォーマンス
    -   `ReadPool`を使用して読み取りパフォーマンスを最適化し、 `raw_get/get/batch_get`を 30% 増加します
    -   メトリクスのパフォーマンスを向上させる
    -   Raftスナップショット プロセスが完了したら、バランシングを迅速化するために直ちに PD に通知します。
    -   RocksDB のフラッシュによって引き起こされるパフォーマンスのジッターを解決する
    -   データ削除後のスペース再利用メカニズムを最適化する
    -   サーバー起動時のガベージクリーニングを高速化します。
    -   `DeleteFilesInRanges`を使用してレプリカ移行中の I/O オーバーヘッドを削減します。
-   安定性
    -   PD リーダーが切り替わったときに gRPC 呼び出しが返されない問題を修正
    -   スナップショットが原因でノードをオフラインにするのが遅くなる問題を修正
    -   レプリカの移行によって消費される一時スペースの使用量を制限する
    -   長期間リーダーを選出できない地域を報告する
    -   圧縮イベントに応じてリージョンサイズ情報を適時に更新します
    -   リクエストのタイムアウトを避けるためにスキャン ロックのサイズを制限する
    -   OOM を回避するためにスナップショットを受信するときのメモリ使用量を制限する
    -   CIテストの速度を上げる
    -   スナップショットが多すぎることによって引き起こされる OOM 問題を修正する
    -   gRPC の構成`keepalive`
    -   リージョン番号の増加によって引き起こされる OOM 問題を修正

## ティスパーク {#tispark}

TiSpark は別のバージョン番号を使用します。現在の TiSpark バージョンは 1.0 GA です。 TiSpark 1.0 のコンポーネントは、Apache Spark を使用した TiDB データの分散コンピューティングを提供します。

-   TiKV からデータを読み取るための gRPC 通信フレームワークを提供する
-   TiKVコンポーネントデータと通信プロトコルのエンコードとデコードを提供します
-   以下を含む計算プッシュダウンを提供します。
    -   集計プッシュダウン
    -   述語のプッシュダウン
    -   トップNプッシュダウン
    -   リミットプッシュダウン
-   インデックス関連のサポートを提供する
    -   述語をリージョンキー範囲またはセカンダリインデックスに変換します
    -   `Index Only`クエリの最適化 -リージョンにインデックス スキャンをテーブル スキャンに適応的にダウングレードします。
-   コストベースの最適化を提供する
    -   サポート統計
    -   インデックスの選択
    -   ブロードキャストテーブルのコストを見積もる
-   複数の Spark インターフェイスのサポートを提供する
    -   スパークシェルをサポート
    -   ThriftServer/JDBCのサポート
    -   Spark-SQL インタラクションのサポート
    -   PySpark シェルのサポート
    -   SparkRをサポート