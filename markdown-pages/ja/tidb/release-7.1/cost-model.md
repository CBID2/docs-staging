---
title: Cost Model
summary: Learn how the cost model used by TiDB works during physical optimization.
---

# コストモデル {#cost-model}

TiDB はコスト モデルを使用して、 [物理的な最適化](/sql-physical-optimization.md)の際にインデックスと演算子を選択します。このプロセスを次の図に示します。

![CostModel](https://download.pingcap.com/images/docs/cost-model.png)

TiDB は、プラン内の各インデックスのアクセス コストと各物理演算子の実行コスト (HashJoin や IndexJoin など) を計算し、最小コスト プランを選択します。

以下は、コスト モデルがどのように機能するかを説明するための単純化された例です。テーブル`t`があるとします。

```sql
mysql> SHOW CREATE TABLE t;
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                                                                                                        |
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| t     | CREATE TABLE `t` (
  `a` int(11) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  `c` int(11) DEFAULT NULL,
  KEY `b` (`b`),
  KEY `c` (`c`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin |
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

`SELECT * FROM t WHERE b < 100 and c < 100`ステートメントを実行するとき、TiDB は 20 行が`b < 100`条件を満たし、500 行が`c < 100`条件を満たし、 `INT`タイプのインデックスの長さが 8 であると推定するとします。次に、TiDB は 2 つのインデックスのコストを計算します。

-   インデックス`b`のコスト = `b < 100`の行数 * インデックス`b`の長さ = 20 * 8 = 160
-   インデックス`c`のコスト = `c < 100`の行数 * インデックス`c`の長さ = 500 * 8 = 4000

インデックス`b`のコストが低いため、TiDB はインデックスとして`b`を選択します。

前述の例は簡略化されており、基本原理を説明するためにのみ使用されています。実際の SQL 実行では、TiDB コスト モデルはより複雑になります。

## コストモデルバージョン2 {#cost-model-version-2}

TiDB v6.2.0 では、新しいコスト モデルであるコスト モデル バージョン 2 が導入されています。

コスト モデル バージョン 2 は、コスト式のより正確な回帰キャリブレーションを提供し、コスト式の一部を調整し、以前のバージョンのコスト式よりも正確になっています。

コストモデルのバージョンを切り替えるには、 [`tidb_cost_model_version`](/system-variables.md#tidb_cost_model_version-new-in-v620)変数を設定します。

> **ノート：**
>
> コスト モデルのバージョンを切り替えると、クエリ プランが変更される可能性があります。