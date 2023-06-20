---
title: Data Migration Architecture
---

# データ移行アーキテクチャ {#data-migration-architecture}

このドキュメントでは、データ移行 (DM) のアーキテクチャを紹介します。

DM は、DM マスター、DM ワーカー、および dmctl の 3 つのコンポーネントで構成されます。

![Data Migration architecture](https://download.pingcap.com/images/docs/dm/dm-architecture-2.0.png)

## アーキテクチャコンポーネント {#architecture-components}

### DMマスター {#dm-master}

DM マスターは、データ移行タスクの操作を管理し、スケジュールします。

-   DMクラスタのトポロジ情報の保存
-   DMワーカープロセスの実行状態の監視
-   データ移行タスクの実行状態の監視
-   データ移行タスクを管理するための統合ポータルの提供
-   シャーディング シナリオに基づく各インスタンスのシャード テーブルの DDL 移行を調整する

### DMワーカー {#dm-worker}

DM ワーカーは、特定のデータ移行タスクを実行します。

-   binlogデータをローカルstorageに保存する
-   データ移行サブタスクの構成情報の保存
-   データ移行サブタスクの操作を調整する
-   データ移行サブタスクの実行状態の監視

DMワーカーの詳細については、 [DMワーカー紹介](/dm/dm-worker-intro.md)を参照してください。

### dmctl {#dmctl}

dmctl は、DM クラスターを制御するために使用されるコマンド ライン ツールです。

-   データ移行タスクの作成、更新、または削除
-   データ移行タスクの状態を確認する
-   データ移行タスクのエラーの処理
-   データ移行タスクの構成が正しいことを確認する

## アーキテクチャの特徴 {#architecture-features}

### 高可用性 {#high-availability}

複数の DM マスター ノードをデプロイすると、すべての DM マスター ノードが組み込み etcd を使用してクラスターを形成します。 DM マスター クラスターは、クラスター ノード情報やタスク構成などのメタデータを保存するために使用されます。 etcd を通じて選出されたリーダー ノードは、クラスター管理やデータ移行タスク管理などのサービスを提供するために使用されます。したがって、利用可能な DM マスター ノードの数が展開されたノードの半分を超えた場合、DM クラスターは通常はサービスを提供できます。

デプロイされた DM ワーカー ノードの数が上流の MySQL/MariaDB ノードの数を超えると、追加の DM ワーカー ノードはデフォルトでアイドル状態になります。 DM ワーカー ノードがオフラインになるか、DM マスター リーダーから分離されると、DM マスターは元の DM ワーカー ノードの他のアイドル状態の DM ワーカー ノードへのデータ移行タスクを自動的にスケジュールします。 (DM ワーカー ノードが分離されている場合、そのノード上のデータ移行タスクは自動的に停止します)。使用可能なアイドル状態の DM ワーカー ノードがない場合、元の DM ワーカーのデータ移行タスクは、1 つの DM ワーカー ノードがアイドル状態になるまで一時的に停止され、その後タスクは自動的に再開されます。

> **ノート：**
>
> データ移行タスクが完全なエクスポートまたはインポートの処理中である場合、移行タスクは高可用性をサポートしません。主な理由は次のとおりです。
>
> -   完全なエクスポートの場合、MySQL は特定のスナップショット ポイントからのエクスポートをまだサポートしていません。これは、データ移行タスクが再スケジュールまたは再開された後は、前回の中断ポイントからエクスポートを再開できないことを意味します。
>
> -   完全インポートの場合、DM-worker はエクスポートされた完全なデータをノード間で読み取ることをまだサポートしていません。これは、データ移行タスクが新しい DM ワーカー ノードにスケジュールされた後は、スケジュールが実行される前に、元の DM ワーカー ノードでエクスポートされた完全なデータを読み取ることができないことを意味します。