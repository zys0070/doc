# デプロイ

TFF は、計算を定義するほか、それらを実行するためのツールも提供しています。シミュレーションが主な目的ではありますが、提供しているインターフェースとツールはより一般的です。このドキュメントでは、様々な種類のプラットフォームにデプロイするためのオプションについて説明しています。

注意: これは、制作中のドキュメントです。

## 概要

TFF 計算向けのデプロイには、主に 2 つのモードがあります。

- **ネイティブバックエンド**。<a><code>computation.proto</code></a> で定義されるとおり、TFF 計算の構文構造を解釈できる場合、バックエンドを<em>ネイティブ</em>と呼んでいます。ネイティブバックエンドは、必ずしもすべての言語構造体または組み込み関数をサポートする必要はありません。ネイティブバックエンドは、Python コードが消費する <a><code>tff.framework.Executor</code></a> またはそれの言語非依存バージョンとして、gRPC エンドポイントとして公開される <a><code>executor.proto</code></a> に定義された Executer など、標準的な TFF <em>executor</em> インターフェースの 1 つを実装する必要があります。

    上記のインターフェースをサポートするネイティブバックエンドは、ノートブックや実験スクリプトを実行するためなど、デフォルトの参照ランタイムの代わりにインタラクティブに使用することができます。ほとんどのネイティブバックエンドは、*解釈モデル* で実行します。つまり、計算の定義を定義されたとおりに処理し、徐々に実行しますが、必ずしもこのようなケースになる必要はありません。ネイティブバックエンドは、計算の一部を*変換*（*コンパイル*または JIT コンパイル）してパフォーマンスを改善するか、構造を簡略化することもできます。これの一般的な使用例の1つは、計算に現れるフェデレーション演算子のセットを減らし、変換のバックエンドダウンストリームの一部を完全なセットに公開する必要がないようにすることです。

- **非ネイティブバックエンド**。非ネイティブバックエンドは、ネイティブバックエンドとは対照的に、TFF 計算構造を直接解釈することができないため、バックエンドが理解する別の*ターゲット表現*に変換する必要があります。このようなバックエンドの注目すべき例は、Hadoop クラスタ、または静的データパイプラインの同様のプラットフォームです。計算がそのようなバックエンドにデプロイされるには、先に*変換*（または*コンパイル*）される必要があります。セットアップに応じて、これはユーザーに対して透過的に行う（非ネイティブバックエンドは、内部的に変換を実行する [`tff.framework.Executor`](https://www.tensorflow.org/federated/api_docs/python/tff/framework/Executor) などの標準 executor インターフェースにラッピングされる）か、ユーザーが計算を特定のクラスのバックエンドが理解する適切なターゲット表現に手動で変換できるようにするツールとして公開されます。特定の種類の非ネイティブバックエンドをサポートするコードは、[`tff.backends`](https://www.tensorflow.org/federated/api_docs/python/tff/backends) 名前空間にあります。これを執筆している時点では、非ネイティブバックエンドの唯一のサポートタイプは、単一ラウンドの MapReduce を実行できるクラスのシステムです。

## ネイティブバックエンド

詳細は、近日追加されます。

## 非ネイティブバックエンド

### MapReduce

詳細は、近日追加されます。
