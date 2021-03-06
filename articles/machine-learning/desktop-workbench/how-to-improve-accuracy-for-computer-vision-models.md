---
title: Azure Machine Learning でのコンピューター ビジョン モデルと分類モデルの精度の向上
description: Azure ML Package for Computer Vision を使用して、コンピューター ビジョンの画像分類モデル、物体検出モデル、および画像類似度モデルの精度を向上させる方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989313"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>コンピューター ビジョン モデルの精度を向上させる

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

**Azure ML Package for Computer Vision** を使用して、画像分類モデル、物体検出モデル、および画像類似度モデルを構築して配置できます。 このパッケージとそのインストール方法の詳細について説明します。

この記事では、これらのモデルを微調整してそれらの精度を向上させる方法を説明します。 

## <a name="accuracy-of-image-classification-models"></a>画像分類モデルの精度

Computer Vision Package は、さまざまなデータセットで良い結果を示しています。 ほとんどの機械学習プロジェクトでこれは事実ですが、新しいデータセットで最良の結果を得るには、パラメーターを慎重に調整し、さまざまな設計に関する決定を評価する必要があります。 以降のセクションで、特定のデータセットの精度を向上させる方法に関するガイダンスを提供します。つまり、最適化するために最も有望なパラメーターは何か、これらのパラメーターで試す必要がある値は何か、および回避すべき落とし穴は何かについて説明します。

一般に言って、ディープ ラーニング モデルのトレーニングでは、最短のトレーニング時間とモデルの最高の精度は両立しません。 Computer Vision Package には、事前設定された既定のパラメーターがあります (後述の表の 1 行目を参照してください)。これらは、通常は高精度のモデルを高速のトレーニングで生成することに重点を置いています。 多くの場合、精度は、より高い画像の解像度やより深いモデルなどを使用することで向上させることができますが、代償としてトレーニング時間が 10 倍以上増加します。

既定のパラメーターを使用してモデルのトレーニングを開始し、その結果を調べ、必要に応じて検証済みのアノテーションを修正した後でのみ、トレーニング時間が長くなるパラメーターを試すことをお勧めします (後述の表で推奨するパラメーターの値を参照してください)。 これらのパラメーターを理解することは技術的には必要ではありませんが、理解しておくことをお勧めします。


### <a name="best-practices-and-tips"></a>ベスト プラクティスとヒント

* データ品質: トレーニング セットとテスト セットは高品質である必要があります。 つまり、画像には正しく注釈が付けられ、あいまいな画像 (人間の眼で見てもテニス ボールなのかレモンなのかわからない画像など) は除去され、属性は相互に排他的である (各画像は 1 つの属性にのみ属している) 必要があります。

* DNN を調整する前に、SVM 分類子をトレーニング済みの固定された DNN を使用して特徴抽出器としてトレーニングしておく必要があります。 これは Computer Vision Package サポートされています。DNN 自体が変更されることはないため、長時間のトレーニングは必要ありません。 この単純な方法でも、多くの場合、高い精度に達するため、強力なベースラインになります。 次の手順は、さらに精度が高まるように DNN を調整することです。

* 画像内の興味のある物体が小さい場合、画像分類はうまく機能しないことがわかっています。 このような場合は、Computer Vision Package の Tensorflow に基づく Faster R-CNN などの物体検出の使用を検討してください。

* トレーニング データは、多ければ多いほどよいです。 目安として、クラスごとに少なくとも 100 個のサンプルを用意する必要があります。つまり、"犬" として 100 個の画像を、"猫" として 100 個の画像を用意します。モデルのトレーニングは少ない画像でも実行できますが、良い結果が得られない可能性があります。

* トレーニング画像は、GPU を搭載したコンピューターの (HDD ではなく) SSD ドライブ上にローカルに配置されている必要があります。 それ以外の場合、画像を読み取るための待機時間によって、トレーニングの速度が大幅に低下する可能性があります (100 倍も低下することがあります)。


### <a name="parameters-to-optimize"></a>最適化するためのパラメーター

これらのパラメーターの最適値を見つけることが重要であり、多くの場合、精度を大幅に向上させることができます。
* 学習率 (`lr_per_mb`): ほぼ間違いなく、目的を達成するための最も重要なパラメーターです。 DNN 調整後に設定されたトレーニングの精度が 5% まで上昇する場合、最も可能性が高いのは、学習率が高すぎるか、トレーニング エポックの数が少なすぎることです。 特に、小規模なデータセットでは、DNN は、トレーニング データを過学習する傾向があり、実際にはテスト セットに適したモデルに至ります。 通常は、初期学習率が 2 回減少する 15 のエポックが使用されます。場合によっては、より多くのエポックを使用してトレーニングすると、パフォーマンスが向上する可能性があります。

* 入力解像度 (`image_dims`): 既定の画像解像度は 224 x 224 ピクセルです。 500 x 500 ピクセルや 1000 x 1000 ピクセルなどの高い画像解像度を使用すると、大幅に精度が向上する可能性がありますが、DNN の調整の速度が低下します。 Computer Vision Package が予期している入力解像度は、タプル (カラー チャネルの数、画像の幅、画像の高さ) です。たとえば (3, 224, 224) では、カラー チャネルの数を 3 (赤帯域、緑帯域、青帯域) に設定する必要があります。

* モデルのアーキテクチャ (`base_model_name`): 既定の ResNet-18 モデルではなく、ResNet-34 や ResNet-50 などのより深い DNN の使用を試します。 Resnet-50 モデルは、深いだけでなく、最後から 2 番目のレイヤーの出力が、2048 個の浮動小数点数値になります (ResNet-18 および ResNet-34 モデルの 512 個の浮動小数点数値に対して)。 この次元の増加は、DNN を固定したまま SVM 分類子をトレーニングする場合に、特に有益である可能性があります。

* ミニバッチ サイズ (`mb_size`): ミニバッチ サイズが大きければ大きいほどトレーニングは高速になりますが、代償として DNN のメモリ消費量が増加します。 そのため、より深いモデル (例: ResNet-50 対 ResNet-18 ) や高い画像解像度 (500 \* 500 ピクセル対 224 \* 224 ピクセル) を選択する場合、通常は、メモリ不足エラーを回避するためにミニバッチ サイズを小さくする必要があります。 ミニバッチ サイズを変更するときは、多くの場合、次の表に示すように、学習率も調整する必要があります。
* ドロップアウト率 (`dropout_rate`) と L2 正規化 (`l2_reg_weight`): DNN の過学習は、ドロップアウト レート率 0.5 (Computer Vision Package の既定値は 0.5) 以上を使用し、正規化の重み (Computer Vision Package の既定値は 0.0005) を増加させることで減らすことができます。 ただし、特にデータセットが小規模な DNN では、過学習を回避するのは困難であり、多くの場合、可能ではありません。


### <a name="parameter-definitions"></a>パラメーターの定義

- **学習率**: 勾配降下法による学習中に使用するステップ サイズ。 設定値が低すぎると、モデルがトレーニングするエポック数が増え、高すぎると、モデルは適切なソリューションに収束しません。 通常は、特定のエポック数の後で学習率を減少させるスケジュールが使用されます。 たとえば、学習率スケジュール `[0.05]*7 + [0.005]*7 + [0.0005]` は、最初の 7 つのエポックで初期学習率 0.05 を使用した後、別の 7 つのエポックで 10 倍減少させた学習率 0.005 を使用し、最後に、1 つのエポックで 100 倍減少させた学習率 0.0005 を使用してモデルを微調整することに相当します。

- **ミニバッチ サイズ**: GPU は、複数の画像を並列処理して、計算を高速化できます。 これらの並列処理された画像は、ミニバッチとも呼ばれます。 ミニバッチ サイズが大きければ大きいほどトレーニングは高速になりますが、代償として DNN のメモリ消費量が増加します。

### <a name="recommended-parameter-values"></a>推奨されるパラメーター値

次の表は、さまざまな画像分類タスクで高精度のモデルを生成することが証明された複数のパラメーター セットを示しています。 最適のパラメーターは、特定のデータセットと使用される正確な GPU に依存するため、この表はガイドラインとしてのみ参照する必要があります。 これらのパラメーターを試した後、500 x 500 ピクセルを超える画像解像度や、Resnet-101 や Resnet-152. などのより深いモデルも検討してください。

表の 1 行目は、Computer Vision Package に設定されている既定のパラメーターに対応します。 その他のすべての行の設定では、トレーニング時間が長くなります (1 列目に示されています) が、精度が向上するというメリットがあります (2 列目の 3 つの内部データセットに対する平均精度を参照してください)。 たとえば、最後の行のパラメーターでは、トレーニング時間が 5 ～ 15 倍長くなりますが、3 つの内部テスト セットに対する (平均) 精度が 82.6% から 92.8% に向上します。

モデルの深さと入力解像度の高さが増加すればするほど、消費される DNN メモリが増加するため、ミニバッチ サイズを減らして (モデルの複雑さが増加します)、メモリ不足エラーを回避する必要があります。 次の表に示されているように、ミニバッチ サイズを 2 倍減らすときは、常に学習率も同じ乗数で減らすことをお勧めします。 メモリ容量が小さい GPU では、ミニバッチ サイズをさらに減らさなければならない場合があります。

| トレーニング時間 (概算) | 精度の例 | ミニバッチ サイズ (*mb_size*) | 学習率 (*lr_per_mb*) | 画像解像度 (*image_dims*) | DNN アーキテクチャ (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 倍 (基準) | 82.6 % | 32 | [0.05]\*7  + [0.005]\*7  + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 ～ 5 倍    | 90.2% | 16 | [0.025]\*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 ～ 5 倍    | 87.5% | 16 | [0.025]\*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 ～ 15 倍        | 92.8% |  8 | [0.01]\*7  + [0.001]\*7  + [0.0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>次の手順

Azure Machine Learning Package for Computer Vision: の詳細:
+ リファレンス ドキュメントを参照します。

+ [Azure Machine Learning 用の他の Python パッケージ](reference-python-package-overview.md)を確認します。