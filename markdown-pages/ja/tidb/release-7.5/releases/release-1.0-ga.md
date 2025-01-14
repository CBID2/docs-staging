---
title: TiDB 1.0 release notes
---

# TiDB 1.0 リリースノート {#tidb-1-0-release-notes}

2017 年 10 月 16 日に、TiDB 1.0 がリリースされました。このリリースは、MySQL の互換性、SQL の最適化、安定性、パフォーマンスに重点を置いています。

## TiDB {#tidb}

-   SQL クエリ オプティマイザー:
    -   コストモデルを調整する
    -   プッシュダウンを分析する
    -   関数シグネチャのプッシュダウン
-   内部データ形式を最適化して中間データサイズを削減
-   MySQLの互換性を強化する
-   `NO_SQL_CACHE`構文をサポートし、storageエンジンでのキャッシュの使用を制限します。
-   Hash Aggregator オペレーターをリファクタリングしてメモリ使用量を削減する
-   Stream Aggregator オペレーターのサポート

## PD {#pd}

-   読み取りフローベースのバランシングをサポート
-   ストアの重量と重量ベースのバランシングの設定をサポート

## TiKV {#tikv}

-   コプロセッサーがより多くのプッシュダウン関数をサポートするようになりました
-   サンプリング操作のプッシュダウンをサポート
-   データコンパクトを手動でトリガーしてスペースを迅速に収集することをサポートします
-   パフォーマンスと安定性の向上
-   デバッグ用の Debug API を追加する
-   TiSpark ベータ版リリース:
-   サポート構成フレームワーク
-   ThriftSever/JDBC および Spark SQL をサポート

## 了承 {#acknowledgement}

### 以下の企業およびチームに感謝します {#special-thanks-to-the-following-enterprises-and-teams}

-   アルコン
-   モバイク
-   サムスン電子
-   スピーディクラウド
-   テンセントクラウド
-   ユークラウド

### 以下の組織および個人のオープンソース ソフトウェアおよびサービスに感謝します。 {#thanks-to-the-open-source-software-and-services-from-the-following-organizations-and-individuals}

-   アスタ・シェ
-   CNCF
-   コアOS
-   データブリック
-   ドッカー
-   ギットハブ
-   グラファナ
-   gRPC
-   ジェプセン
-   Kubernetes
-   なまず
-   プロメテウス
-   レッドハット
-   RocksDB チーム
-   ラストチーム

### 個々の貢献者に感謝します {#thanks-to-the-individual-contributors}

-   8cbx
-   須田章裕
-   アリックス
-   アルストン111111
-   アンエルフ
-   アンディ・リブリアン
-   アーサー・ヤン
-   アスタキシー
-   バイ、ヤン
-   バイラオヘ
-   ビン・リウ
-   コスモスのせい
-   ブリーズウィッシュ
-   カルロス・フェレイラ
-   セ・ガオ
-   チャン・チャンジャン
-   チェン・リアン
-   胡コレラ
-   チューチャオ
-   冷水
-   コール・R・ローレンス
-   クイキウ
-   翠源
-   クウェン
-   大港
-   デビッド・チェン
-   デビッド・ディン
-   ダサい
-   ダカデヴィル
-   蕭弟子
-   ディタン
-   ディスク化
-   東秀
-   ドリームクスター
-   ドロゴン
-   杜川
-   ディラン・ウェン
-   イーボーイ
-   エリック・ロマーノ
-   ユアン・チョウ
-   フィイージオ
-   愚痴
-   フレッド・ワン
-   ファッド
-   フダリ
-   ガオヤンシャオジュ
-   ゴグス
-   ゴルーチン
-   グレゴリー・イアン
-   ルー・グアンクン
-   ギリェルメ・ヒュブナー・フランコ
-   謝海斌
-   ハンフェイ
-   ホーキングレイ
-   中村宏明
-   ヒウィジド
-   ワン・ホンユアン
-   胡明
-   胡子明
-   ホァチャオ・ファン
-   徐淮宇
-   ハクスリー・フー
-   iamxy
-   イアン
-   内部
-   iroi44
-   イワン・ヤン
-   ジャック・ユウ
-   ジャッキー・リュー
-   ヤン・メルクル
-   ジェイソン・W
-   ジェイ
-   ジェイ・リー
-   王建飛
-   梁嘉興
-   周潔
-   ジンヘリン
-   ジョナサン・ブール
-   カール・オステンドルフ
-   クナーフェ
-   クイバ
-   雷雪春
-   リー
-   李世海
-   リャオ・チャン
-   ライト
-   麗江
-   リリアン・リー
-   リキュール リブラジー
-   リウ・コン
-   劉少輝
-   liubo0127
-   リャナン
-   lkk2003rty
-   ルイ
-   ルイシュスト
-   ラッキーカラー
-   リン
-   メー・ファン
-   マイヤン
-   マクスウェル
-   孟上旗
-   マイケル・ベレンチェンコ
-   モジー
-   もっと凍結する
-   MQ
-   mxlxm
-   ニール・シェン
-   ネットロビー
-   ンガウト
-   ニコール・ニー
-   ノールーシュ
-   オンリーメルブ
-   オーバーヴィーナス
-   パラディンティリオン
-   ポール
-   プリヤ・セス
-   クグシャオザン
-   クソン
-   黔南
-   キウケレン
-   秋葉水峰
-   クィニーピンキャップ
-   クペン
-   レイン・リー
-   蘭暁龍
-   レイ
-   リック・ユウ
-   日陰の
-   ショーンリー
-   シェン・リー
-   シェン・タン
-   シャーリー
-   シュアイ・リー
-   修寧
-   ワン・シュユ
-   シドンタン
-   サイレンパー
-   サイモン・J・マッド
-   サイモン・シア
-   スキムミルク6877
-   sllt
-   スープ
-   スフィンクス
-   ステフェン
-   サムバグ
-   サンハオ2017
-   タオ・メン
-   周陶
-   テニス
-   ティエンチャイアマオ
-   田光裕
-   トリスタン・スー
-   叡州
-   UncP
-   不明
-   v01dstar
-   バン
-   王翔USTC
-   ワンヤンジュン
-   ワンギソン1996
-   平日
-   ヴェーゲル
-   魏福
-   シャオ・ウェンビン
-   ウェンティン・リー
-   シー・ウェンシュアン
-   ウィンキャオ
-   木こり
-   ウクスエリアン
-   シャン・リー
-   シャオジャン・カイ
-   楊玄家
-   玄武
-   徐淮嶼
-   ヤン・ゼシュアン
-   ヤン・オーティシエ
-   チェン・ヤンツェ
-   崔宜鼎
-   イム
-   ようようふ
-   ユジュン
-   ユウェン・シェン
-   李ゼジュン
-   張裕寧
-   張金鵬1987
-   趙逸軍
-   ヤン・ジェシュアン
-   鄭銭
-   ジェンチェンファン
-   正望波
-   胡志峰
-   鄭志源
-   周濤
-   チョウバードブルー
-   周寧南
-   ヤン・ツィイー
-   zs634134578
-   zxylvlp
-   ジグアン
-   ジーザス・ジェイソン
