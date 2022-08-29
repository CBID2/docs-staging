---
title: Maintain a TiFlash Cluster
summary: Learn common operations when you maintain a TiFlash cluster.
---

# TiFlash クラスターを管理する {#maintain-a-tiflash-cluster}

このドキュメントでは、TiFlash のバージョンの確認など、 [ティフラッシュ](/tiflash/tiflash-overview.md)クラスタを維持する際の一般的な操作を実行する方法について説明します。このドキュメントでは、重要なログと TiFlash のシステム テーブルも紹介します。

## TiFlashのバージョンを確認する {#check-the-tiflash-version}

TiFlash のバージョンを確認するには、次の 2 つの方法があります。

-   TiFlashのバイナリファイル名が`tiflash`の場合、 `./tiflash version`コマンドを実行することでバージョンを確認できます。

    ただし、上記のコマンドを実行するには、 `libtiflash_proxy.so`の動的ライブラリを含むディレクトリ パスを`LD_LIBRARY_PATH`の環境変数に追加する必要があります。これは、TiFlash の実行が`libtiflash_proxy.so`ダイナミック ライブラリに依存しているためです。

    たとえば、 `tiflash`と`libtiflash_proxy.so`が同じディレクトリにある場合、最初にこのディレクトリに切り替えてから、次のコマンドを使用して TiFlash のバージョンを確認できます。

    
    ```shell
    LD_LIBRARY_PATH=./ ./tiflash version
    ```

-   TiFlashのログからTiFlashのバージョンを確認してください。ログのパスについては、 [`tiflash.toml`ファイル](/tiflash/tiflash-configuration.md#configure-the-tiflashtoml-file)の`[logger]`の部分を参照してください。例えば：

    ```
    <information>: TiFlash version: TiFlash 0.2.0 master-375035282451103999f3863c691e2fc2
    ```

## TiFlash の重要なログ {#tiflash-critical-logs}

| ログ情報                                                                                                                                 | ログの説明                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------- |
| `[INFO] [<unknown>] ["KVStore: Start to persist [region 47, applied: term 6 index 10]"] [thread_id=23]`                              | データの複製が開始されます (ログの先頭にある角括弧内の数字はスレッド ID を示します)            |
| `[DEBUG] [<unknown>] ["CoprocessorHandler: grpc::Status DB::CoprocessorHandler::execute(): Handling DAG request"] [thread_id=30]`    | DAG リクエストの処理、つまり、TiFlash がコプロセッサ リクエストの処理を開始します。         |
| `[DEBUG] [<unknown>] ["CoprocessorHandler: grpc::Status DB::CoprocessorHandler::execute(): Handle DAG request done"] [thread_id=30]` | DAG リクエストの処理が完了しました。つまり、TiFlash がコプロセッサ リクエストの処理を終了しました。 |

コプロセッサー要求の開始または終了を見つけ、ログの開始時に出力されたスレッド ID を使用して、コプロセッサー要求の関連ログを見つけることができます。

## TiFlash システム表 {#tiflash-system-table}

`information_schema.tiflash_replica`システム テーブルの列名とその説明は次のとおりです。

| カラム名            | 説明                                   |
| --------------- | ------------------------------------ |
| TABLE_SCHEMA    | データベース名                              |
| TABLE_NAME      | テーブル名                                |
| TABLE_ID        | テーブル ID                              |
| REPLICA_COUNT   | TiFlash レプリカの数                       |
| LOCATION_LABELS | リージョン内のどの複数のレプリカが分散しているかに基づく PD のヒント |
| 利用可能            | 利用可能かどうか (0/1)                       |
| 進捗              | レプリケーションの進行状況 [0.0~1.0]              |
