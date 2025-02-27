---
title: Data Factory の使用事例 - 製品推奨
description: Azure Data Factory と他のサービスを使用して実装した使用事例について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 44c66f5fa89e7293667c930bfd1720d72dc26bf4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785072"
---
# <a name="use-case---product-recommendations"></a>使用事例 - 製品に関する推奨事項
Azure Data Factory は、ソリューション アクセラレータの Cortana Intelligence Suite の実装に使用されている数多くあるサービスの 1 つです。  このスイートの詳細については、 [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) のページを参照してください。 このドキュメントでは、Azure ユーザーが Azure Data Factory と他の Cortana Intelligence コンポーネント サービスを使用して、既に解決および実装している一般的な使用事例について説明します。

## <a name="scenario"></a>シナリオ
一般的に、オンライン小売業者は、顧客が最も興味を持ち、結果として購入する可能性が最も高い製品を提示することで、顧客の購入意欲を高めます。 そのために、個人用に設定された製品推奨を使用して、顧客のオンライン エクスペリエンスをカスタマイズする必要があります。 個人用に設定された推奨は、ユーザーの現在および過去の購入行動データ、製品情報、新規取り扱いのブランド、製品と顧客のセグメント データを基に作成されます。  さらに、全ユーザーの全体的な使用行動の分析に基づいて、ユーザーに製品推奨を提供することができます。

オンライン小売業者の目標は、ユーザーのクリックから売上への転換を最適化し、売上額を向上することです。  そのためには、顧客の関心と操作に基づいて、状況と行動に応じた製品推奨を提供する必要があります。 この使用事例では、ビジネスの一例として、顧客に合わせて最適化したいオンライン小売業者を取り上げています。 しかし、ここで説明する原則は、個人用に設定された製品推奨を使用して商品やサービスに対する顧客の関心を引き、顧客の購入エクスペリエンスを強化したいと考えているすべての企業に適用できます。

## <a name="challenges"></a>課題
オンライン小売業者がこのような使用事例を実装するときに直面する課題は数多くあります。 

第 1 に、オンプレミスとクラウドに保存されている複数のデータ ソースから、さまざまな規模と形のデータを取り込みます。 このデータには、製品データ、顧客の行動履歴データ、ユーザーがオンライン小売店サイトを閲覧するときのユーザー データが含まれます。 

第 2 に、個人用に設定された製品推奨を合理的かつ正確に計算し、予測する必要があります。 オンライン小売業者は、製品、ブランド、顧客行動データ、ブラウザー データに加え、過去の顧客から寄せられたフィードバックも考慮して、ユーザーにとって最適な製品推奨を決定する必要があります。 

第 3 に、シームレスな閲覧と購入のエクスペリエンス、最新で関連性が高い推奨を提供するために、推奨は即時にユーザーに示す必要があります。 

最後に、小売業者は、全体的なアップセルとクロスセルについてクリックからの転換の成功を追跡して、アプローチの効果を測定し、調整して今後の推奨に生かす必要があります。

## <a name="solution-overview"></a>ソリューションの概要
この使用事例は、実在の Azure ユーザーが解決および実装したものです。このユーザーは、Azure Data Factory と他の Cortana Intelligence コンポーネント サービス ([HDInsight](https://azure.microsoft.com/services/hdinsight/) や [Power BI](https://powerbi.microsoft.com/) など) を使用しています。

このオンライン小売業者は、ワークフロー全体のデータ ストレージ オプションとして、Azure BLOB ストア、オンプレミスの SQL Server、Azure SQL Database、リレーショナル データ マートを使用しています。  BLOB ストアには、顧客情報、顧客行動データ、および製品情報データが含まれています。 製品情報データには、オンプレミスの Azure Synapse Analytics に格納された製品ブランド情報と製品カタログが含まれています。 

すべてのデータは結合され、製品推奨システムに取り込まれます。ユーザーが Web サイトで製品を閲覧すると、顧客の関心と操作に基づいて個人用に設定された推奨が提供されます。 また、特定の顧客に関連付けられていない Web サイト全体の使用パターンに基づいて、顧客が閲覧している製品に関連する製品も表示されます。

![使用事例の図](./media/data-factory-product-reco-usecase/diagram-1.png)

オンライン小売業者の Web では、毎日 GB 単位の未加工の Web ログ ファイルが半構造ファイルとして生成されます。 未加工の Web ログ ファイルと、顧客および製品のカタログ情報は、Data Factory のグローバルにデプロイされたサービスとしてのデータ移動機能を使用して、Azure BLOB ストレージに定期的に取り込まれます。 長期保存のために、1 日の未加工のログ ファイルは (年月別に) 分割され、BLOB ストレージに保存されます。  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) は、未加工のログ ファイルを分割し、BLOB ストレージに保存し、取り込んだログを Hive および Pig スクリプトを使用して処理するために使用されます。 その後、分割された Web ログ データは、機械学習推奨システムに必要な入力を抽出するために処理され、個人用に設定された製品推奨が生成されます。

この例の機械学習に使用されている推奨システムは、 [Apache Mahout](https://mahout.apache.org/)製のオープン ソースの機械学習推奨プラットフォームです。  任意の [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) またはカスタム モデルをシナリオに適用することができます。  Mahout モデルは、全体的な使用パターンに基づいて Web サイトのアイテム間の類似性を推測し、個々のユーザーに基づいて個人用に設定された推奨を生成するために使用されます。

最後に、個人用に設定された製品の推奨事項の結果セットは、リレーショナル データ マートに移動され、小売業者の Web サイトに使用されます。  また、別のアプリケーションで BLOB ストレージから結果セットに直接アクセスしたり、別の消費者と使用事例のために他のストアに結果セットを移動したりすることもできます。

## <a name="benefits"></a>メリット
ソリューションの製品推奨戦略を最適化し、経営目標に合わせて調整することで、オンライン小売業者の販売とマーケティングの目標を達成することができます。 また、この小売業者は、効率的で信頼性とコスト効果が高い方法で製品推奨ワークフローを運用および管理できるようになりました。 そのアプローチにより、クリックから売上への転換の成功の測定に基づいて、モデルを更新し、効果を微調整しやすくなりました。 小売業者は Azure Data Factory を使用することで、時間と費用がかかる手動のクラウド リソース管理を止め、オンデマンドのクラウド リソース管理に移行できました。 そして、時間と費用を節約し、ソリューションのデプロイメントにかかる時間を短縮することができました。 Azure ポータルから実行できる Data Factory の監視および管理の UI で、データ系列ビューと運用サービスのヘルス状態の確認とトラブルシューティングが簡単になりました。 完了データが確実に生成され、ユーザーに提供されるように、ソリューションをスケジュールできるようになりました。また、データと処理の依存関係は自動的に管理されるようになり、人の操作は不要になりました。

オンライン小売業者は、この個人用に設定されたショッピング エクスペリエンスを提供することで、より競争力が高く、魅力的なカスタマー エクスペリエンスを作り、売上と全体的な顧客満足度を向上しています。

