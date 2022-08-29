---
title: Configuration Options
summary: Learn the configuration options in TiDB.
---

# Configuration / コンフィグレーションオプション {#configuration-options}

TiDBクラスタを起動すると、コマンドライン オプションまたは環境変数を使用して構成できます。このドキュメントでは、TiDB のコマンド オプションを紹介します。デフォルトの TiDB ポートは、クライアント リクエスト用に`4000` 、ステータス レポート用に`10080`です。

## <code>--advertise-address</code> {#code-advertise-address-code}

-   TiDB サーバーへのログインに使用する IP アドレス
-   デフォルト: `""`
-   このアドレスは、TiDBクラスタの残りの部分とユーザーがアクセスできる必要があります。

## <code>--config</code> {#code-config-code}

-   構成ファイル
-   デフォルト: `""`
-   構成ファイルを指定した場合、TiDB は構成ファイルを読み取ります。対応する構成がコマンド ライン オプションにも存在する場合、TiDB はコマンド ライン オプションの構成を使用して、構成ファイル内の構成を上書きします。詳細な構成情報については、 [TiDBConfiguration / コンフィグレーションファイルの説明](/tidb-configuration-file.md)を参照してください。

## <code>--config-check</code> {#code-config-check-code}

-   構成ファイルの有効性を確認して終了します
-   デフォルト: `false`

## <code>--config-strict</code> {#code-config-strict-code}

-   構成ファイルの有効性を強制します
-   デフォルト: `false`

## <code>--cors</code> {#code-cors-code}

-   TiDB HTTP ステータス サービスの Cross-Origin Request Sharing (CORS) リクエストに`Access-Control-Allow-Origin`の値を指定します
-   デフォルト: `""`

## <code>--host</code> {#code-host-code}

-   TiDB サーバーが監視するホスト アドレス
-   デフォルト: `"0.0.0.0"`
-   TiDB サーバーはこのアドレスを監視します。
-   `"0.0.0.0"`のアドレスは、デフォルトですべてのネットワーク カードを監視します。複数のネットワーク カードがある場合は、サービスを提供するネットワーク カードを`192.168.100.113`などで指定します。

## <code>--enable-binlog</code> {#code-enable-binlog-code}

-   TiDB バイナリログ生成を有効または無効にします
-   デフォルト: `false`

## <code>-L</code> {#code-l-code}

-   ログレベル
-   デフォルト: `"info"`
-   オプションの値: `"debug"` 、 `"info"` 、 `"warn"` 、 `"error"` 、 `"fatal"`

## <code>--lease</code> {#code-lease-code}

-   スキーマ リースの期間。よくわからないまま値を変更するのは**危険**です。
-   デフォルト: `45s`

## <code>--log-file</code> {#code-log-file-code}

-   ログファイル
-   デフォルト: `""`
-   このオプションが設定されていない場合、ログは &quot;stderr&quot; に出力されます。このオプションが設定されている場合、ログは対応するファイルに出力されます。

## <code>--log-slow-query</code> {#code-log-slow-query-code}

-   スロー クエリ ログのディレクトリ
-   デフォルト: `""`
-   このオプションが設定されていない場合、ログはデフォルトで`--log-file`で指定されたファイルに出力されます。

## <code>--metrics-addr</code> {#code-metrics-addr-code}

-   Prometheus プッシュゲートウェイのアドレス
-   デフォルト: `""`
-   空のままにすると、Prometheus クライアントのプッシュが停止します。
-   フォーマットは`--metrics-addr=192.168.100.115:9091`です。

## <code>--metrics-interval</code> {#code-metrics-interval-code}

-   Prometheus クライアントのプッシュ間隔 (秒単位)
-   デフォルト: `15s`
-   値を 0 に設定すると、Prometheus クライアントのプッシュが停止します。

## <code>-P</code> {#code-p-code}

-   TiDB サービスの監視ポート
-   デフォルト: `"4000"`
-   TiDB サーバーは、このポートからの MySQL クライアント要求を受け入れます。

## <code>--path</code> {#code-path-code}

-   「unistore」などのローカル ストレージ エンジンのデータ ディレクトリへのパス
-   `--store = tikv`の場合、パスを指定する必要があります。 `--store = unistore`の場合、パスを指定しない場合はデフォルト値が使用されます。
-   TiKV のような分散ストレージ エンジンの場合、 `--path`は実際の PD アドレスを指定します。 PD サーバーを 192.168.100.113:2379、192.168.100.114:2379、および 192.168.100.115:2379 にデプロイすると仮定すると、 `--path`の値は「192.168.100.113:2379、192.168.100.114:22079、8.195」です。 .
-   デフォルト: `"/tmp/tidb"`
-   `tidb-server --store=unistore --path=""`を使用して、純粋なメモリ内 TiDB を有効にすることができます。

## <code>--proxy-protocol-networks</code> {#code-proxy-protocol-networks-code}

-   [プロキシ プロトコル](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)を使用して TiDB に接続できるプロキシ サーバーの IP アドレスのリスト。
-   デフォルト: `""`
-   一般に、リバース プロキシの背後にある TiDB にアクセスすると、TiDB はリバース プロキシ サーバーの IP アドレスをクライアントの IP アドレスとして取得します。 PROXY プロトコルを有効にすることで、HAProxy などのこのプロトコルをサポートするリバース プロキシは、実際のクライアント IP アドレスを TiDB に渡すことができます。
-   このフラグを構成した後、TiDB は、構成されたソース IP アドレスが PROXY プロトコルを使用して TiDB に接続できるようにします。 PROXY 以外のプロトコルが使用されている場合、この接続は拒否されます。このフラグを空のままにすると、PROXY プロトコルを使用して IP アドレスが TiDB に接続できなくなります。値は、IP アドレス (192.168.1.50) または CIDR (192.168.1.0/24) で、区切り記号として`,`を使用できます。 `*`は任意の IP アドレスを意味します。

> **警告：**
>
> `*`を使用すると、任意の IP アドレスのクライアントがその IP アドレスを報告できるようになり、セキュリティ上のリスクが生じる可能性があるため、注意して使用してください。さらに、 `*`を使用すると、TiDB に直接接続する内部コンポーネント (TiDB ダッシュボードなど) が使用できなくなる可能性もあります。

## <code>--proxy-protocol-header-timeout</code> {#code-proxy-protocol-header-timeout-code}

-   PROXY プロトコル ヘッダー読み取りのタイムアウト
-   デフォルト: `5` (秒)

    > **ノート：**
    >
    > 値を`0`に設定しないでください。特別な場合を除き、デフォルト値を使用してください。

## <code>--report-status</code> {#code-report-status-code}

-   ステータス レポートと pprof ツールを有効 ( `true` ) または無効 ( `false` ) にします
-   デフォルト: `true`
-   `true`に設定すると、このパラメーターはメトリクスと pprof を有効にします。 `false`に設定すると、このパラメーターはメトリクスと pprof を無効にします。

## <code>--run-ddl</code> {#code-run-ddl-code}

-   `tidb-server`が DDL ステートメントを実行するかどうかを確認し、クラスタで`tidb-server`の数が 2 つを超える場合に設定します
-   デフォルト: `true`
-   値は (true) または (false) です。 (true) は、 `tidb-server`が DDL 自体を実行することを示します。 (false) は、 `tidb-server`が DDL 自体を実行しないことを示します。

## <code>--socket string</code> {#code-socket-string-code}

-   TiDB サービスは、外部接続に unix ソケット ファイルを使用します。
-   デフォルト: `""`
-   `/tmp/tidb.sock`を使用して UNIX ソケット ファイルを開きます。

## <code>--status</code> {#code-status-code}

-   TiDB サーバーのステータス レポート ポート
-   デフォルト: `"10080"`
-   このポートは、サーバーの内部データを取得するために使用されます。データには[プロメテウスの指標](https://prometheus.io/)と[プロフ](https://golang.org/pkg/net/http/pprof/)が含まれます。
-   Prometheus メトリクスには`"http://host:status_port/metrics"`でアクセスできます。
-   pprof データは`"http://host:status_port/debug/pprof"`でアクセスできます。

## <code>--status-host</code> {#code-status-host-code}

-   TiDB サービスのステータスを監視するために使用される`HOST`
-   デフォルト: `0.0.0.0`

## <code>--store</code> {#code-store-code}

-   最下層で TiDB が使用するストレージ エンジンを指定します。
-   デフォルト: `"unistore"`
-   「unistore」または「tikv」を選択できます。 (「unistore」はローカル ストレージ エンジン、「tikv」は分散ストレージ エンジン)

## <code>--token-limit</code> {#code-token-limit-code}

-   TiDB で同時に実行できるセッションの数。交通整理に使用しています。
-   デフォルト: `1000`
-   同時セッション数が`token-limit`より大きい場合、リクエストはブロックされ、完了した操作がトークンを解放するのを待ちます。

## <code>-V</code> {#code-v-code}

-   TiDBのバージョンを出力します
-   デフォルト: `""`

## <code>--plugin-dir</code> {#code-plugin-dir-code}

-   プラグインの格納ディレクトリ。
-   デフォルト: `"/data/deploy/plugin"`

## <code>--plugin-load</code> {#code-plugin-load-code}

-   ロードするプラグインの名前。それぞれをカンマで区切ります。
-   デフォルト: `""`

## <code>--affinity-cpus</code> {#code-affinity-cpus-code}

-   コンマで区切られた TiDB サーバーの CPU アフィニティを設定します。たとえば、「1,2,3」です。
-   デフォルト: `""`

## <code>--repair-mode</code> {#code-repair-mode-code}

-   データ修復シナリオでのみ使用される修復モードを有効にするかどうかを決定します。
-   デフォルト: `false`

## <code>--repair-list</code> {#code-repair-list-code}

-   修復モードで修復されるテーブルの名前。
-   デフォルト: `""`
