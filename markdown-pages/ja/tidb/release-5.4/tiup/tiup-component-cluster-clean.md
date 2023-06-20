---
title: tiup cluster clean
---

# tiup cluster clean {#tiup-cluster-clean}

テスト環境では、クラスタをデプロイしたばかりの状態にリセットする必要がある場合があります。これは、すべてのデータを削除することを意味します。 `tiup cluster clean`コマンドを使用して簡単に行うことができます。実行後、クラスタを停止し、クラスタデータを削除します。クラスタを手動で再起動すると、クリーンなクラスタが得られます。

> **警告：**
>
> このコマンドは、ログのクリーンアップのみを選択した場合でも、最初にクラスタを停止します。したがって、実稼働環境では使用しないでください。

## 構文 {#syntax}

```shell
tiup cluster clean <cluster-name> [flags]
```

`<cluster-name>`はクリーンアップするクラスタです。

## オプション {#options}

### - 全て {#all}

-   データとログを同時にクリーンアップします。 `--data`と`--log`を同時に指定するのと同じです。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。
-   指定されていない場合は、少なくとも次のいずれかのオプションを指定する必要があります。
    -   --data：データをクリーンアップします
    -   --log：ログをクリーンアップします

### - データ {#data}

-   データをクリーンアップします。 `--all`も指定されていない場合、データはクリーンアップされません。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

### - ログ {#log}

-   ログをクリーンアップします。 `--all`も指定されていない場合、ログはクリーンアップされません。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

### --ignore-node {#ignore-node}

-   クリーニングが不要なノードを指定します。複数のノードを指定するには、このオプションを複数回使用できます。たとえば、 `--ignore-node <node-A> --ignore-node <node-B>` 。
-   データ型： `StringArray`
-   デフォルト：空

### --無視-役割 {#ignore-role}

-   クリーニングを必要としない役割を指定します。複数の役割を指定するには、このオプションを複数回使用できます。たとえば、 `--ignore-role <role-A> --ignore-role <role-B>` 。
-   データ型： `StringArray`
-   デフォルト：空

### -h、-help {#h-help}

-   ヘルプ情報を出力します。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

## 出力 {#output}

tiup-clusterの実行ログ。

[&lt;&lt;前のページに戻る-TiUPクラスターコマンドリスト](/tiup/tiup-component-cluster.md#command-list)