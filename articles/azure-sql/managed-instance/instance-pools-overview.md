---
title: Azure SQL Managed Instance プールとは
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance プール (プレビュー) について説明します。これは、より小規模な SQL Server データベースをクラウドに大規模に移行して複数のマネージド インスタンスを管理するための便利でコスト効率に優れた方法を提供する機能です。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347066"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Azure SQL Managed Instance プール (プレビュー) とは
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

より小規模な SQL Server インスタンスを大規模にクラウドに移行するための便利でコスト効率に優れた方法を提供する、Azure SQL Managed Instance のインスタンス プールです。

インスタンス プールを使用すると、すべての移行要件に従ってコンピューティング リソースを事前にプロビジョニングすることができます。 その後、事前にプロビジョニングされたコンピューティング レベルまで、個別のマネージド インスタンスを複数デプロイできます。 たとえば、8 個の仮想コアを事前にプロビジョニングする場合は、2 つの 2 vCore インスタンスと 1 つの 4 vCore インスタンスをデプロイしてから、これらのインスタンスにデータベースを移行できます。 インスタンス プールが利用できるようになる前は、より小規模でそれほどコンピューティング処理を必要としないワークロードは、クラウドに移行するときに、より大規模なマネージド インスタンスに統合しなければならないことがよくありました。 データベースのグループを大規模なインスタンスに移行する必要がある場合は、通常、慎重な容量計画とリソース管理、追加のセキュリティの考慮事項、インスタンス レベルでの何らかの追加のデータ統合作業が必要でした。

さらに、インスタンス プールではネイティブ VNet 統合がサポートされているため、同じサブネット内に複数のインスタンス プールと複数の単一インスタンスをデプロイできます。

## <a name="key-capabilities"></a>主な機能

インスタンス プールには、次のような利点があります。

1. 2 vCore インスタンスをホストする機能。 *"\*インスタンス プール内のインスタンスに対してのみ*"。
2. 予測可能で高速なインスタンスのデプロイ時間 (最大 5 分)。
3. 最小 IP アドレスの割り当て。

次の図は、仮想ネットワーク サブネット内に複数のマネージド インスタンスがデプロイされているインスタンス プールを示しています。

![複数のインスタンスを持つインスタンス プール](./media/instance-pools-overview/instance-pools1.png)

インスタンス プールを使用すると、同じ仮想マシン上に複数のインスタンスをデプロイすることができます。仮想マシンのコンピューティング サイズは、プールに割り当てられている仮想コアの合計数に基づきます。 このアーキテクチャでは、仮想マシンを複数のインスタンスに "*パーティション分割*" できます。2 個の仮想コアを含む、サポート対象の任意のサイズに分割できます (2 vCore インスタンスはプール内のインスタンスでのみ使用できます)。

最初のデプロイが行われると、プール内のインスタンスに対する管理操作は、はるかに高速になります。 これは、[仮想クラスター](connectivity-architecture-overview.md#high-level-connectivity-architecture) (仮想マシンの専用セット) のデプロイまたは拡張が、マネージド インスタンスのプロビジョニングの一部ではないからです。

プール内のすべてのインスタンスは同じ仮想マシンを共有するため、IP の割り当ての合計はデプロイされたインスタンスの数に依存しません。これは、狭い IP 範囲のサブネットへのデプロイに便利です。

各プールには、9 つの IP アドレスのみの固定 IP 割り当てがあります (サブネット独自のニーズ用に予約されている 5 つの IP アドレスは含まれません)。 詳細については、[単一インスタンスに対するサブネット サイズの要件](vnet-subnet-determine-size.md)に関するページを参照してください。

## <a name="application-scenarios"></a>アプリケーションのシナリオ

次の一覧に、インスタンス プールを考慮する必要がある主なユース ケースを示します。

- ほとんどが小規模なサイズ (たとえば 2 vCore または 4 vCore) の "*SQL Server インスタンスのグループ*" の同時移行。
- "*予測可能で短いインスタンスの作成またはスケーリング*" が重要なシナリオ。 たとえば、インスタンスレベルの機能を必要とするマルチテナント SaaS アプリケーション環境に新しいテナントをデプロイする場合です。
- "*固定費*" または "*使用制限*" があることが重要なシナリオ。 たとえば、固定サイズ (またはほとんどサイズが変化しない) の共有の開発/テストまたはデモ環境を実行していて、必要に応じてマネージド インスタンスを定期的にデプロイする場合です。
- VNet サブネットで "*最小 IP アドレスの割り当て*" が重要なシナリオ。 プール内のすべてのインスタンスは仮想マシンを共有しているため、割り当てられた IP アドレスの数は、単一インスタンスの場合よりも少なくなります。

## <a name="architecture"></a>Architecture

インスタンス プールは、通常の ("*単一の*") マネージド インスタンスと同様のアーキテクチャを備えています。 [Azure 仮想ネットワーク内のデプロイ](../../virtual-network/virtual-network-for-azure-services.md)をサポートし、お客様に分離とセキュリティを提供するため、インスタンス プールでは[仮想クラスター](connectivity-architecture-overview.md#high-level-connectivity-architecture)にも依存しています。 仮想クラスターは、お客様の仮想ネットワーク サブネット内にデプロイされている分離された仮想マシンの専用セットを表します。

2 つのデプロイ モデルの主な違いは次のとおりです。インスタンス プールは同じ仮想マシン ノード上で複数の SQL Server プロセスをデプロイでき、[Windows ジョブ オブジェクト](/windows/desktop/ProcThread/job-objects)を使用して管理されるリソースであるのに対し、単一インスタンスは仮想マシン ノード上に常に 1 つだけです。

次の図は、1 つのインスタンス プールと、同じサブネット内にデプロイされた 2 つの個別のインスタンスを示し、両方のデプロイ モデルの主要なアーキテクチャの詳細を示しています。

![インスタンス プールと 2 つの個別のインスタンス](./media/instance-pools-overview/instance-pools2.png)

すべてのインスタンス プールは、その下に別の仮想クラスターが作成されます。 プール内のインスタンスと、同じサブネット内にデプロイされた単一インスタンスは、SQL Server プロセスとゲートウェイ コンポーネントに割り当てられたコンピューティング リースを共有しません。これにより、パフォーマンスの予測可能性が確保されます。

## <a name="resource-limitations"></a>リソースの制限事項

プール内のインスタンス プールとインスタンスに関して、いくつかのリソース制限があります。

- インスタンス プールは、Gen5 ハードウェアでのみ使用できます。
- プール内のマネージド インスタンスには専用の CPU と RAM があるため、すべてのインスタンスでの仮想コアの合計数は、そのプールに割り当てられている仮想コアの数以下になる必要があります
- [インスタンス レベルの制限](resource-limits.md#service-tier-characteristics)はすべて、プール内に作成されたインスタンスに適用されます
- インスタンスレベルの制限に加えて、次の 2 つの制限も "*インスタンス プール レベルで*" 適用されます。
  - プールあたりの合計ストレージ サイズ (8 TB)。
  - プールあたりのユーザー データベースの合計数。 この制限は、プールの仮想コア値によって異なります。
    - 8 個の仮想コアのプールでは、最大 200 個のデータベースがサポートされます。
    - 16 個の仮想コアのプールでは、最大 400 個のデータベースがサポートされます。
    - 24 個以上の仮想コアのプールでは、最大 500 個のデータベースがサポートされます。
- AAD 管理者は、インスタンス プール内にデプロイされたインスタンスに対して設定できないため、AAD 認証は使用できません。

すべてのインスタンスのストレージの割り当てとデータベースの数の合計は、インスタンス プールによってさらされる制限以下にする必要があります。

- インスタンス プールでは、8、16、24、32、40、64、および 80 の仮想コアがサポートされています。
- プール内のマネージド インスタンスでは、2、4、8、16、24、32、40、64、80 の仮想コアがサポートされています。
- プール内のマネージド インスタンスでは、次の場合を除き、32 GB から 8 TB のストレージ サイズがサポートされます。
  - 2 つの仮想コア インスタンスでは、32 GB から 640 GB のサイズがサポートされます。
  - 4 つの仮想コア インスタンスでは、32 GB から 2 TB のサイズがサポートされます。
- プール内のマネージド インスタンスには、インスタンスあたり最大 100 個のユーザー データベースの制限があります。ただし、インスタンスあたり最大 50 個のユーザー データベースをサポートする、2 つの仮想コア インスタンスは除きます。

[サービス レベルのプロパティ](resource-limits.md#service-tier-characteristics)は、インスタンス プール リソースに関連付けられているため、プール内のすべてのインスタンスは、プールのサービス レベルと同じサービス レベルである必要があります。 現時点では、General Purpose サービス レベルのみを使用できます (現在のプレビューの制限事項については、次のセクションを参照してください)。

### <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

パブリック プレビューには、次の制限事項があります。

- 現時点では、General Purpose サービス レベルのみを使用できます。
- パブリック プレビュー中にインスタンス プールをスケーリングすることができないため、デプロイの前に慎重に容量計画を策定することが重要です。
- Azure portal では、インスタンス プールの作成および構成のサポートはまだ利用できません。 インスタンス プールに対するすべての操作は、PowerShell でのみサポートされています。 また、事前に作成されたプールでの初回のインスタンス デプロイも、PowerShell でのみサポートされています。 プールにデプロイされると、Azure portal を使用してマネージド インスタンスを更新できます。
- プールの外部で作成されたマネージド インスタンスを既存のプールに移動することはできません。さらに、プール内に作成されたインスタンスを、単一インスタンスとしてプールの外側に移動することも、別のプールに移動することもできません。
- [予約容量](../database/reserved-capacity-overview.md)インスタンスの価格については、提供されていません。

## <a name="sql-features-supported"></a>サポートされている SQL 機能

プール内に作成されたマネージド インスタンスでは、[単一マネージド インスタンスでサポートされているのと同じ互換性レベルと機能](sql-managed-instance-paas-overview.md#sql-features-supported)がサポートされます。

プールにデプロイされたすべてのマネージド インスタンスには、SQL エージェントの個別のインスタンスがあります。

オプションの機能、または特定の値 (インスタンスレベルの照合順序、タイム ゾーン、データ トラフィックのパブリック エンドポイント、フェールオーバー グループなど) を選択する必要がある機能は、インスタンス レベルで構成され、プール内のインスタンスごとに異なる場合があります。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

プール内のマネージド インスタンスには専用の仮想コアと RAM がありますが、ローカル ディスク (tempdb 使用のため) とネットワーク リソースを共有します。 可能性は低いですが、プール内の複数のインスタンスでリソース消費量が同時に増加すると、"*ノイジー ネイバー*" (うるさい隣人) の影響を受ける可能性があります。 この動作を確認した場合は、これらのインスタンスをより大きなプールにデプロイするか、単一インスタンスとしてデプロイすることを検討してください。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

プールにデプロイされたインスタンスは同じ仮想マシンを共有するので、セキュリティ上のリスクが高い機能を無効にするか、これらの機能へのアクセス許可をしっかりと制御することを検討してください。 たとえば、CLR 統合、ネイティブ バックアップと復元、データベース メールなどです。

## <a name="instance-pool-support-requests"></a>インスタンス プールのサポート要求

[Azure portal](https://portal.azure.com) でインスタンス プールのサポート要求を作成および管理します。

インスタンス プールのデプロイ (作成または削除) に関連する問題が発生している場合は、 **[問題のサブタイプ]** フィールドで **[インスタンス プール]** を指定してください。

![インスタンス プールのサポート リクエスト](./media/instance-pools-overview/support-request.png)

プール内で単一マネージド インスタンスまたはデータベースに関連する問題が発生している場合は、Azure SQL Managed Instance の通常のサポート チケットを作成する必要があります。

インスタンス プールの有無にかかわらず、より大きな SQL Managed Instance のデプロイを作成するには、より大きなリージョン クォータを取得することが必要な場合があります。 詳細については、「[Azure SQL Database のクォータの増加を要求する](../database/quota-increase-request.md)」を参照してください。 インスタンス プールのデプロイ ロジックによって "*プール レベルでの*" 仮想コアの合計消費量がご利用のクォータと比較され、そのクォータをさらに増やすことなく新しいリソースの作成を許可するかどうかが決定されることにご注意ください。

## <a name="instance-pool-billing"></a>インスタンス プールの課金

インスタンス プールを使用すると、コンピューティングとストレージを個別にスケーリングできます。 お客様は、仮想コアで測定されるプール リソースに関連付けられているコンピューティング、およびギガバイト単位で測定されるすべてのインスタンスに関連付けられているストレージ (最初の 32 GB はすべてのインスタンスで無料です) に対して料金を支払います。

プールの仮想コア価格は、そのプールにデプロイされているインスタンスの数に関係なく課金されます。

コンピューティング価格 (仮想コア単位) では、次の 2 つの価格オプションを使用できます。

  1. *ライセンス込み*:SQL Server ライセンスの料金が含まれます。 これは、ソフトウェア アシュアランス付きの既存の SQL Server ライセンスを適用しないお客様を対象としています。
  2. *[Azure ハイブリッド特典]* : SQL Server 向けの Azure ハイブリッド特典を含む割引価格。 お客様は、ソフトウェア アシュアランス付きの既存の SQL Server ライセンスを使用してこの価格を選択することができます。 資格情報とその他の詳細については、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)に関するページを参照してください。

プール内の個々のインスタンスに対して、異なる価格オプションを設定することはできません。 親プール内のすべてのインスタンスは、ライセンス込みの価格または Azure ハイブリッド特典の価格である必要があります。 プールのライセンス モデルは、プールの作成後に変更できます。

> [!IMPORTANT]
> インスタンスにプールとは異なるライセンス モデルを指定すると、プールの価格が使用され、インスタンス レベルの値は無視されます。

[開発テスト特典の対象となるサブスクリプション](https://azure.microsoft.com/pricing/dev-test/)でインスタンス プールを作成すると、Azure SQL Managed Instance で最大 55% の割引料金が自動的に受けられます。

インスタンス プールの価格の詳細については、[SQL Managed Instance の価格のページ](https://azure.microsoft.com/pricing/details/sql-database/managed/)で "*インスタンス プール*" のセクションを参照してください。

## <a name="next-steps"></a>次のステップ

- インスタンス プールの使用を開始するには、[SQL Managed Instance プールの攻略ガイド](instance-pools-configure.md)を参照してください。
- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](instance-create-quickstart.md)を参照してください。
- 機能比較一覧については、[Azure SQL 共通機能](../database/features-comparison.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
- 組み込みのトラブルシューティング インテリジェンスを使用した SQL Managed Instance データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Managed Instance の監視](../../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
- 料金情報については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
