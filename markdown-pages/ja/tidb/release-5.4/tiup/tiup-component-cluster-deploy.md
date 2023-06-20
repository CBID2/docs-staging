---
title: tiup cluster deploy
---

# tiup cluster deploy {#tiup-cluster-deploy}

`tiup cluster deploy`コマンドは、新しいクラスタをデプロイするために使用されます。

## 構文 {#syntax}

```shell
tiup cluster deploy <cluster-name> <version> <topology.yaml> [flags]
```

-   `<cluster-name>` ：新しいクラスタの名前。既存のクラスタ名と同じにすることはできません。
-   `<version>` ：デプロイするTiDBクラスタのバージョン番号（ `v5.4.2`など）。
-   `<topology.yaml>` ：準備された[トポロジーファイル](/tiup/tiup-cluster-topology-reference.md) 。

## オプション {#options}

### -u、-user {#u-user}

-   ターゲットマシンへの接続に使用されるユーザー名を指定します。このユーザーは、ターゲットマシンに対するシークレットフリーのsudoroot権限を持っている必要があります。
-   データ型： `STRING`
-   デフォルト：コマンドを実行する現在のユーザー。

### -i、-identity_file {#i-identity-file}

-   ターゲットマシンへの接続に使用されるキーファイルを指定します。
-   データ型： `STRING`
-   コマンドでこのオプションが指定されていない場合、デフォルトでは`~/.ssh/id_rsa`ファイルがターゲットマシンへの接続に使用されます。

### -p、-password {#p-password}

-   ターゲットマシンへの接続に使用するパスワードを指定します。このオプションを`-i/--identity_file`と同時に使用しないでください。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

### --ignore-config-check {#ignore-config-check}

-   このオプションは、構成チェックをスキップするために使用されます。コンポーネントのバイナリファイルが展開された後、TiDB、TiKV、およびPDコンポーネントの構成が`<binary> --config-check <config-file>`を使用してチェックされます。 `<binary>`は、デプロイされたバイナリファイルのパスです。 `<config-file>`は、ユーザー構成に基づいて生成された構成ファイルです。
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。
-   デフォルト：false

### --ラベルなし {#no-labels}

-   このオプションは、ラベルチェックをスキップするために使用されます。
-   2つ以上のTiKVノードが同じ物理マシンにデプロイされている場合、リスクが存在します。PDはクラスタトポロジを学習できないため、PDはリージョンの複数のレプリカを1つの物理マシン上の異なるTiKVノードにスケジュールし、この物理マシンを単一にします。点。このリスクを回避するために、ラベルを使用して、同じリージョンを同じマシンにスケジュールしないようにPDに指示できます。ラベルの設定については、 [トポロジラベルによるレプリカのスケジュール](/schedule-replicas-by-topology-labels.md)を参照してください。
-   テスト環境では、このリスクが問題になる可能性があり、 `--no-labels`を使用してチェックをスキップできます。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

### --skip-create-user {#skip-create-user}

-   クラスタの展開中に、tiup-clusterはトポロジファイルに指定されたユーザー名が存在するかどうかを確認します。そうでない場合は、作成します。このチェックをスキップするには、 `--skip-create-user`オプションを使用できます。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

### -h、-help {#h-help}

-   ヘルプ情報を出力します。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

## 出力 {#output}

展開ログ。

[&lt;&lt;前のページに戻る-TiUPクラスターコマンドリスト](/tiup/tiup-component-cluster.md#command-list)