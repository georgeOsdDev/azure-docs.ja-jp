---
title: 多クラスのブースト デシジョン ツリー:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の多クラスのブースト デシジョン ツリー モジュールを使用して、ラベル付きデータを使用して分類器を作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905302"
---
# <a name="multiclass-boosted-decision-tree"></a>多クラスのブースト デシジョン ツリー

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

ブースト デシジョン ツリー アルゴリズムに基づく機械学習モデルを作成するには、このモジュールを使用します。

ブースト デシジョン ツリーは、第 2 のツリーで第 1 のツリーの誤差を補正し、第 3 のツリーで第 1 および第 2 のツリーの誤差を補正するといったアンサンブル学習手法です。 予測は、ツリーの集団に基づいて行われます。

## <a name="how-to-configure"></a>構成方法 

このモジュールでは、トレーニングされていない分類モデルが作成されます。 分類は教師あり学習手法であるため、ラベル列のすべての行に値を含んだ "*ラベル付けされたデータセット*" が必要です。

このタイプのモデルは、"[モデルのトレーニング](././train-model.md)" を使用してトレーニングできます。 

1.  **多クラスのブースト デシジョン ツリー** モジュールをパイプラインに追加します。

1.  **[Create trainer mode]\(トレーナー モードの作成\)** オプションを設定して、モデルのトレーニング方法を指定します。

    + **Single Parameter (単一パラメーター)** : モデルの構成方法がわかっている場合、特定の値のセットを引数として渡すことができます。
    
    + **[Parameter Range]\(パラメーター範囲\)** : 最適なパラメーターがわからず、パラメーター スイープを実行する場合は、このオプションを選択します。 反復する値の範囲を選択します。[モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md)では、指定した設定の可能なすべての組み合わせに対して反復処理を行い、最適な結果を生成するハイパーパラメーターを決定します。  

1. **[Maximum number of leaves per tree]\(ツリーあたりの最大リーフ数\)** は、ツリーに作成できる終端ノード (リーフ) の最大数を制限します。
    
    この値を増やすと、ツリーのサイズが大きくなって精度が上がる反面、オーバーフィットが発生したり、トレーニング時間が長くなったりするおそれがあります。
  
1. **[Minimum number of samples per leaf node]\(リーフ ノードごとの最小サンプル数\)** は、ツリーの終端ノード (リーフ) を作成するうえで必要なケース数を指定します。  

    この値を増やすと、新しいルールを作成するためのしきい値が大きくなります。 たとえば、既定値の 1 では、ケースが 1 つであっても新しいルールを作成できます。 この値を 5 に増やした場合、同じ条件を満たすケースがトレーニング データに少なくとも 5 つ含まれている必要があります。

1. **[学習速度]** は、学習時のステップ サイズを定義します。 0 から 1 までの数値を入力してください。

    学習器がどの程度の速さ (遅さ) で最適解に収束するかは、学習速度によって決まります。 ステップ サイズが大きすぎると、最適解から離れていってしまう可能性があります。 ステップ サイズが小さすぎると、トレーニングが最適解に収束するまでの時間が長くなります。

1. **[Number of trees constructed]\(構築するツリーの数\)** は、集団として作成するデシジョン ツリーの総数を指定します。 作成するデシジョン ツリーを増やすと、カバレッジが向上する可能性はありますが、トレーニング時間が長くなります。

1. **[Random number seed]\(乱数シード\)** に、ランダム シード値として使用する値 (負でない整数) を必要に応じて設定します。 シードを指定することによって、同じデータとパラメーターで繰り返し実行したときの再現性が確保されます。  

    ランダム シードは、既定では 42 に設定されています。 異なるランダム シードを使用して連続実行すると、都度異なる結果を得ることができます。

1. モデルをトレーニングします。

    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Single Parameter]\(単一パラメーター\)** に設定した場合は、タグ付けされたデータセットと [モデルのトレーニング](train-model.md) モジュールを接続します。  
  
    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Parameter Range]\(パラメーター範囲\)** に設定した場合は、[[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) を使用して、タグ付けしたデータセットを接続してモデルをトレーニングします。  
  
    > [!NOTE]
    > 
    > パラメーター範囲を [[モデルのトレーニング]](train-model.md) に渡すと、単一のパラメーター リストの既定値のみが使用されます。  
    > 
    > [[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) モジュールによって、パラメーターごとに設定の範囲が求められているとき、それに単一のパラメーター値セットを渡した場合、それらの値は無視され、学習器の既定値が使用されます。  
    > 
    > **[Parameter Range]\(パラメーター範囲\)** オプションを選択し、任意のパラメーターに単一の値を入力した場合、指定した単一の値はスイープ全体で使用されます。これは、他のパラメーターが値の範囲の中で変化する場合でも同様です。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
