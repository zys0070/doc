# XLA アーキテクチャ

<div style="width:50%; margin:auto; margin-bottom:10px; margin-top:20px;"> <img style="width:50%" src="./images/xlalogo.png">
</div>

## XLAを開発した理由とは

XLA を TensorFlow と連携させるに当たって、以下のようないくつかの目標がありました。

- *実行速度の改善。*サブグラフをコンパイルして存続期間の短い Op の実行時間を短縮することで、TensorFlowランタイムからのオーバーヘッドを排除し、パイプライン演算を融合してメモリオーバーヘッドを削減し、既知のテンソル形状に特化して、より積極的な定数伝播を可能にします。

- *メモリ使用率の改善。*原則として、多くの中間ストレージバッファを排除することで、メモリ使用量の分析とスケジュールを行います。

- *カスタム Op への依存の抑制。*自動的に融合された低レベル Op のパフォーマンスを改善し、手動で融合されたカスタム Op のパフォーマンスに一致させることで、多数のカスタム Op の必要性を除きます。

- *モバイルフットプリントの削減。*サブグラフを事前にコンパイルし、別のアプリケーションに直接リンクできるオブジェクト/ヘッダーファイルのペアを発行することで、TensorFlow ランタイムを排除します。これにより、モバイル推論のフットプリントを数桁削減できます。

- *移植性の改善。*新しいハードウェア用の新しいバックエンドを比較的簡単に作成できるようにします。その時点で、TensorFlow プログラムの大部分がそのハードウェア上で変更されずに実行されます。これは、個々のモノリシック Op を新しいハードウェアに特化するアプローチとは対照的であり、その場合、これらの Op を使用するために TensorFlow プログラムを書き直す必要があります。

## XLA の仕組み

XLA への入力言語は「HLO IR」または単に HLO（High Level Operations: 高レベル演算）と呼ばれています。HLO のセマンティクスは[演算セマンティクス](./operation_semantics.md) ページで説明されています。HLO を[コンパイラ IR](https://www.tensorflow.org/?hl=en)として考えるのが最もわかりやすいでしょう。

XLA は HLO で定義されたグラフ（「コンピュテーション」）を取り、それらを様々なアーキテクチャで使用できる機械指示にコンパイルします。別のバックエンドに移して[新たな HW アーキテクチャで動作させる](https://www.tensorflow.org/xla/jit)ことが容易であるという点で、XLAはモジュール化されていると言えます。x64 や AMR64 用の CPU バックエンドや NVIDIA GPU バックエンドは、TensorFlow のソースツリーで参照できます。

次の図では、XLAの内部で行われているコンパイル処理を示しています。

<div style="width:95%; margin:auto; margin-bottom:10px; margin-top:20px;"><img src="./images/how-does-xla-work.png"></div>

XLAには、[CSE](https://en.wikipedia.org/wiki/Common_subexpression_elimination)、ターゲットに依存しない演算の融合、コンピュテーションにランタイムメモリを割り当てるためのバッファ分析など、ターゲットに依存しないいくつかの最適化と分析パスがあります。

ターゲットに依存しないステップの後、XLA は HLO コンピュテーションをバックエンドに送信します。バックエンドはさらに HLO レベルの最適化を行いますが、ここではターゲットに特化した情報とニーズを念頭において実行されます。たとえば、XLA GPU バックエンドは、GPU プログラミングモデルに特に有用な演算融合を実行し、計算をストリームに分割する方法を決定することがあります。この段階では、バックエンドは特定の演算またはそれによって生じた組み合わせを最適化されたライブラリ呼び出しに対してにパターンマッチングすることもあります。

次のステップは、ターゲット固有のコードの生成です。XLA に含まれる CPU と GPU のバックエンドは [LLVM](https://www.tensorflow.org/xla/developing_new_backend) を使用して、低レベル IR、最適化、およびコード生成を行います。これらのバックエンドは XLA HLO コンピュテーションを効率的に表現するために必要な LLVM IR を発行し、その上で、LLVM を呼び出して、この LLVM IR からネイティブコードを発行します。

GPU バックエンドは現在、LLVM NVPTX バックエンド経由で NVIDIA をサポートしています。CPU バックエンドは複数の CPU ISA をサポートしています。