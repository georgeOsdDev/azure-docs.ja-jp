---
title: Azure IoT Hub を使用したデバイス管理の概要 | Microsoft Docs
description: Azure IoT Hub でのデバイス管理の概要 - エンタープライズ デバイスのライフサイクルとデバイス管理パターン (再起動、出荷時の設定にリセット、ファームウェア更新、構成、デバイス ツイン、クエリ、ジョブなど)。
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 9ff8ebeaf7c4a219bc403c81dfea62f4657a1fd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148334"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub を使用したデバイス管理の概要

Azure IoT Hub には、デバイスやバックエンドの開発者が堅牢なデバイス管理ソリューションを構築するために使用できる機能や拡張モデルが用意されています。 デバイスは、リソースの制約の大きいセンサーをはじめ、専用マイクロコントローラー、デバイス グループの通信をルーティングする強力なゲートウェイなど、多岐にわたります。  また、用途や IoT オペレーターの要件は業界によってかなり異なります。  このような違いがありながら、IoT Hub によるデバイス管理で提供される機能、パターン、およびコード ライブラリは、多様なデバイスとエンド ユーザーに対応することができます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

優れたエンタープライズ IoT ソリューションを構築するために不可欠な要素は、オペレーターがそのデバイス コレクションを継続的に管理するための手法について、戦略を定めることです。 IoT のオペレーターには、シンプルで信頼性が高く、かつ、より戦略的な観点からその仕事に取り組むことのできるツールとアプリケーションが必要です。 この記事では、次について説明します。

* デバイス管理に対する Azure IoT Hub のアプローチの概要。
* 一般的なデバイス管理の原則の説明。
* デバイスのライフサイクルの説明。
* 一般的なデバイス管理パターンの概要。

## <a name="device-management-principles"></a>デバイス管理の原則

IoT のデバイス管理には特有の課題が伴います。このため、すべてのエンタープライズ クラスのソリューションが、以下の原則に対応する必要があります。

![デバイス管理の原則を示す図](media/iot-hub-device-management-overview/image4.png)

* **スケールとオートメーション**: IoT ソリューションには、日々の作業を自動化して比較的少人数の運用スタッフで何百万台というデバイスを管理できるシンプルなツールが必要です。 日常的な業務としてオペレーターは、多数のデバイスを遠隔から一括で操作し、直接の対応が必要な問題が発生した場合にだけ通知を受け取りたいと考えます。

* **開放性と互換性**: デバイスのエコシステムはきわめて多様です。 数多くのデバイス クラス、プラットフォーム、プロトコルに管理ツールを適合させる必要があります。 オペレーターは、制約が最も厳しいシングル プロセスの組み込みチップから、フル機能の高性能コンピューターまで、さまざまな種類のデバイスをサポートできなければなりません。

* **コンテキスト認識**: IoT の環境は動的で絶えず変化しています。 サービスの信頼性は最優先課題です。 メンテナンスのダウンタイムで重要な業務に支障が生じたり、危険な状況を招いたりすることを確実に避けるために、次を考慮に入れる必要があります。

    * SLA の保守ウィンドウ
    * ネットワークと電源の状態
    * 使用中のコンディション
    * デバイスの位置情報

* **さまざまなスタッフへの情報提供**: IoT 運用スタッフがそれぞれに抱えるワークフローやプロセスに対応することが不可欠となります。 運用スタッフは、社内 IT 部門に課せられた制約に適合しながら、  デバイス運用情報を、監督者やその他の管理職に継続的かつリアルタイムに伝える必要があります。

## <a name="device-lifecycle"></a>デバイスのライフサイクル
すべてのエンタープライズ IoT プロジェクトに共通する一般的なデバイス管理ステージ セットがあります。 Azure IoT では、デバイスのライフサイクルは 5 つのステージで構成されています。

![Azure IoT デバイス のライフサイクルの 5 つのフェーズ: 計画、プロビジョニング、構成、監視、使用中止](./media/iot-hub-device-management-overview/image5.png)

この 5 つの各ステージにデバイス オペレーター要件がいくつかあり、完全なソリューションを提供するには、これを満たさなければなりません。

* **計画**: オペレーターは、デバイスのメタデータ体系を作成できます。これらのプロパティを使用することで、簡単かつ正確に一連のデバイスを照会して一括管理操作の対象にすることができます。 デバイス ツインを使用すると、このデバイスのメタデータをタグやプロパティの形式で保存できます。
  
    *参考資料*: 
    * [デバイス ツインを使ってみる](iot-hub-node-node-twin-getstarted.md)
    * [デバイス ツインを理解する](iot-hub-devguide-device-twins.md)
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **プロビジョニング**: IoT Hub に対して新しいデバイスを安全にプロビジョニングし、デバイスの機能をオペレーターがすぐに検出できるようにします。  IoT Hub ID レジストリを使用して、柔軟性に優れたデバイス ID と資格情報を作成し、ジョブを使用してこの操作を一括で実行します。 デバイス ツインのデバイス プロパティを使ってその機能や状態を報告できるように、デバイスを構築します。
  
    *参考資料*: 
    * [デバイス ID を管理する](iot-hub-devguide-identity-registry.md)
    * [デバイス ID の一括管理](iot-hub-bulk-identity-mgmt.md)
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)

* **構成**: 正常性とセキュリティを維持すると共に、デバイスに対する一括構成変更とファームウェア更新を効率化します。 こうしたデバイス管理操作は、Desired プロパティ、またはダイレクト メソッドとブロードキャスト ジョブを使用して一括で実行します。
  
    *参考資料*:
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [多数の IoT デバイスの構成と監視](./iot-hub-automatic-device-management.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **監視**: デバイス コレクション全体の正常性や、進行中の操作の状態を監視して、対処が必要な問題があればオペレーターに通知します。  デバイス ツインを適用して、デバイスが稼働状況と更新操作の状態をリアルタイムで報告できるようにします。 デバイス ツインのクエリを使用して緊急を要する問題を報告するための強力なダッシュボード レポートを作成します。
  
    *参考資料*: 
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
    * [多数の IoT デバイスの構成と監視](./iot-hub-automatic-device-management.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **使用中止**: 故障後やアップグレード サイクルの終了後、またはサービス有効期間の経過後に、デバイスを交換またはデバイスの使用を停止します。  デバイス ツインを使用して、物理デバイスが交換されている場合は、デバイス情報を保持します。使用中止になっている場合はアーカイブされます。 IoT Hub ID レジストリを使用して、デバイス ID と資格情報を安全に失効します。
  
    *参考資料*: 
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [デバイス ID を管理する](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>デバイス管理パターン

以下に示したのは、IoT Hub によって実現される一連のデバイス管理パターンです。 [デバイス管理のチュートリアル](iot-hub-node-node-device-management-get-started.md)では、このパターンを実際のシナリオに合わせて拡張する方法と、こうしたコア テンプレートに基づいて新しいパターンをデザインする方法について詳しく説明しています。

* **再起動**: バックエンド アプリが、再起動を開始したことを、ダイレクト メソッドを通じてデバイスに伝えます。  デバイスは、報告されるプロパティを使用して、その再起動状態を更新します。
  
    ![デバイス管理の再起動パターンを示す図](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **出荷時の設定にリセット**: バックエンド アプリが、出荷時の設定へのリセットを開始したことを、ダイレクト メソッドを通じてデバイスに伝えます。 デバイスは、報告されるプロパティを使用して、そのデバイスの出荷時の設定へのリセット状態を更新します。
  
    ![デバイス管理の出荷時の設定へのリセット パターンを示す図](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **構成** - バックエンド アプリでは、必要なプロパティを使用して、デバイス上で実行されているソフトウェアが構成されます。 デバイスは、報告されるプロパティを使用して、その構成状態を更新します。
  
    ![デバイス管理の構成パターンを示す図](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **ファームウェアの更新プログラム**: バックエンド アプリケーションでは、自動デバイス管理構成を使用して更新プログラムを受け取るデバイスが選択され、更新プログラムを受け取る場所がデバイスに通知され、更新プロセスが監視されます。 デバイスは、複数のステップから成るプロセスを開始してファームウェア イメージをダウンロード、検証し、それを適用して、デバイスを再起動して IoT Hub サービスに再接続します。 この複数のステップから成るプロセスの間、デバイスは、報告されるプロパティを使用して、デバイスの進行状況と状態を更新します。
  
    ![デバイス管理のファームウェア更新パターンを示す図](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **進行状況とステータスの報告**: ソリューション バックエンドによって、一連のデバイス対してデバイス ツイン クエリが実行され、デバイス上で実行されている操作のステータスと進行状況が報告されます。
  
    ![デバイス管理の進行状況とステータスの報告パターンを示す図](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>次の手順

IoT Hub でデバイス管理用に提供される機能、パターン、コード ライブラリを使用することで、デバイスのライフサイクル ステージごとのエンタープライズ IoT オペレーター要件を満たす IoT アプリケーションを作成できます。

IoT Hub のデバイス管理機能についての学習を続けるには、[デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)に関するチュートリアルをご覧ください。