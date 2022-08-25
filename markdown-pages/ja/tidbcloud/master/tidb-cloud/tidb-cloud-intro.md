---
title: TiDB Cloud Introduction
summary: Learn about TiDB Cloud and its architecture.
category: intro
---

# TiDB Cloudの紹介 {#tidb-cloud-introduction}

[TiDB Cloud](https://pingcap.com/products/tidbcloud)は、完全に管理されたサービスとしてのデータベース (DBaaS) であり、TiDB のすべての優れた機能をクラウドにもたらし、データベースの複雑さではなく、アプリケーションに集中できるようにします。

TiDB Cloudの詳細については、次のビデオをご覧ください。

<iframe width="600" height="450" src="https://www.youtube.com/embed/skCV9BEmjbo" title="TiDB クラウドを選ぶ理由" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## TiDB Cloudを選ぶ理由 {#why-tidb-cloud}

-   フルマネージド TiDB サービス

    使いやすい Web ベースの管理プラットフォームを使用して、数回クリックするだけで TiDB クラスターをデプロイ、スケーリング、および管理します。

-   マルチクラウドのサポート

    クラウド ベンダー ロックインなしで柔軟性を維持します。 TiDB Cloudは現在、AWS と GCP で利用でき、さらに多くのプラットフォームが準備中です。

-   高い耐障害性

    ミッション クリティカルなアプリケーションのビジネス継続性を確保するために、データは複数のアベイラビリティ ゾーンに複製され、毎日バックアップされます。

-   生産性向上

    数回クリックするだけで、 TiDB Cloudで簡単に展開、運用、監視できるため、生産性が向上します。

-   エンタープライズ グレードのセキュリティ

    専用のネットワークとマシンでデータをセキュリティし、転送中と保管中の両方で暗号化をサポートします。

-   ワールドクラスのサポート

    サポート ポータル、電子メール、チャット、またはビデオ会議を通じて、同じ世界クラスのサポートを受けてください。

-   シンプルな料金プラン

    隠れた料金のない透明な前払い料金で、使用した分だけお支払いください。

## 建築 {#architecture}

![TiDB Cloud architecture](https://download.pingcap.com/images/docs/tidb-cloud/tidb-cloud-architecture.png)

-   TiDB VPC (仮想プライベート クラウド)

    各TiDB Cloudクラスタでは、すべての TiDB ノードと補助ノード ( TiDB Operatorノード、ログ ノードなどを含む) が独立した VPC にデプロイされます。

-   TiDB Cloudセントラル サービス

    課金、アラート、メタ ストレージ、ダッシュボード UI などのセントラル サービスは、個別に展開されます。ダッシュボード UI にアクセスして、インターネット経由で TiDBクラスタを操作できます。

-   あなたの VPC

    VPC ピアリング接続を介して TiDBクラスタに接続できます。詳細は[VPC ピアリング接続をセットアップする](/tidb-cloud/set-up-vpc-peering-connections.md)を参照してください。
