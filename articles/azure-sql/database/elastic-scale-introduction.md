---
title: スケールアウト
description: これらのツールを使用すると、Software as a Service (SaaS) の開発者は柔軟で拡張性の高いデータベースを簡単に作成できます。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3f00b2c1a8a8264267aa8ae68d80890adeb642ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059147"
---
# <a name="scaling-out-with-azure-sql-database"></a>Azure SQL Database によるスケールアウト
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Elastic Database** ツールを使用すると、Azure SQL Database のデータベースを簡単にスケールアウトできます。 これらのツールと機能では、**Azure SQL Database** のデータベースのリソースを使用して、トランザクションのワークロードに対するソリューション、特にサービスとしてのソフトウェア (SaaS) アプリケーションを作成できます。 Elastic Database は、次の機能で構成されています。

* [Elastic Database クライアント ライブラリ](elastic-database-client-library.md):クライアント ライブラリ機能を使用すると、シャード化されたデータベースを作成および管理できます。  「 [エラスティック データベース ツールの概要](elastic-scale-get-started.md)」を参照してください。
* [Elastic Database 分割/マージ ツール](elastic-scale-overview-split-and-merge.md): シャード化されたデータベース間でデータを移動します。 このツールは、マルチ テナント データベースからシングル テナント データベースに (またはその逆に) データを移動するのに便利です。 「 [エラスティック データベース Split-Merge ツールに関するチュートリアル](elastic-scale-configure-deploy-split-and-merge.md)」を参照してください。
* [Elastic Database ジョブ](elastic-jobs-overview.md) (プレビュー):ジョブを使用して、Azure SQL Database の多数のデータベースを管理します。 ジョブを使用して、スキーマの変更、資格情報の管理、参照データの更新、パフォーマンス データの収集、テナント (顧客) テレメトリの収集などの管理操作を簡単に実行できます。
* [Elastic Database クエリ](elastic-query-overview.md) (プレビュー):複数のデータベースにまたがる Transact-SQL クエリを実行することができます。 これにより、Excel、Power BI、Tableau などのレポート作成ツールに接続できます。
* [エラスティック トランザクション](elastic-transactions-overview.md):この機能を使用すると、複数のデータベースにまたがるトランザクションを実行できます。 Elastic Database トランザクションは、ADO .NET を使用して .NET アプリケーションで利用できます。[System.Transaction クラス](/dotnet/api/system.transactions)を使用することで、これまでに培ったプログラミングの経験を活かすことができます。

次の図は、データベースのコレクションに関連する **Elastic Database の機能** を含めたアーキテクチャを示しています。

この図では、データベースの色は、スキーマを表しています。 同じ色のデータベースは、同じスキーマを共有します。

1. **SQL データベース** が、シャーディング アーキテクチャを使用して Azure でホストされています。
2. **Elastic Database クライアント ライブラリ** は、シャード セットの管理に使用します。
3. 一部のデータベースは、**エラスティック プール** に入っています。 (「[プールとは](elastic-pool-overview.md)」をご覧ください)。
4. **エラスティック データベース ジョブ** は、すべてのデータベースに対して、スケジュールされた、またはアドホックの T-SQL スクリプトを実行します。
5. 1 つのシャードから別のシャードにデータを移動するときには、 **分割/マージ ツール** を使用します。
6. **Elastic Database クエリ** では、シャード セット内のすべてのデータベースにまたがるクエリを記述することができます。
7. **エラスティック トランザクション** では、複数のデータベースにまたがるトランザクションを実行できます。 

![Elastic Database ツール][1]

## <a name="why-use-the-tools"></a>ツールを使用する理由

クラウド アプリケーションにおいて弾力性と拡張性を実現することは、VM と Blob Storage では簡単でした。単純にユニットの追加や削除、能力の増強を行えばよかったからです。 ところが、リレーショナル データベースでのステートフルなデータ処理では、それが依然として困難でした。 これらのシナリオでの課題:

* ワークロードのリレーショナル データベース部分の容量の拡張および縮小。
* 負荷の高いエンド ユーザー (テナント) など、データの特定のサブセットに影響を及ぼす可能性があるホットスポットの管理。

これまで、このようなシナリオには、大規模なサーバーに投資を行ってアプリケーションをサポートすることで、対処してきました。 ただし、この方法には、すべての処理が事前に定義済みのコモディティ ハードウェアで実行されるクラウドでは限りがあります。 そこで、同じ構造を持つ多くのデータベース間でデータを分散し処理を行う手法 ("シャーディング" と呼ばれているスケールアウト パターン) が、コストと弾力性の両面で従来のスケールアップ アプローチの代替案となります。

## <a name="horizontal-and-vertical-scaling"></a>水平および垂直方向のスケーリング

次の図は、水平および垂直方向のスケーリングを示しています。エラスティック データベースのスケーリングでは、どちらも基本的な方法です。

![水平と垂直のスケーリング][2]

水平方向のスケーリングとは、容量または全体のパフォーマンスを調整するためにデータベースを追加または削除することを意味します。"スケールアウト" とも呼ばれます。 シャーディングは、水平方向のスケーリングを実装する一般的な手法で、同じ構造を持つデータベースのコレクションでデータがパーティション分割されます。  

垂直方向のスケーリングとは、個々のデータベースのコンピューティング サイズを増減することを意味します。"スケールアップ" とも呼ばれます。

ほとんどのクラウド規模のデータベース アプリケーションでは、この 2 つの手法を組み合わせて使用しています。 たとえば、サービス アプリケーションとしてのソフトウェアでは、水平方向のスケーリングを使用して新しいエンド ユーザーをプロビジョニングし、垂直方向のスケーリングを使用して、各エンドユーザーのデータベースがワークロードで必要される条件に応じてリソースを拡大縮小できるようにします。

* 水平方向のスケーリングは、 [Elastic Database クライアント ライブラリ](elastic-database-client-library.md)を使用して管理します。
* 垂直方向のスケーリングは、Azure PowerShell コマンドレットを使用してサービス レベルを変更するか、エラスティック プールにデータベースを配置して実行します。

## <a name="sharding"></a>シャーディング

*"シャーディング"* は、同じ構造を持つ大量のデータを数多くの独立したデータベースに分散する手法です。 この手法は、エンド ユーザーまたは企業向けにサービスとしてのソフトウェア (SAAS) 製品をサービスで作成するクラウド開発者の間でよく知られています。 これらのエンド カスタマーは、多くの場合、"テナント" と呼ばれます。 シャーディングは、次のような理由で必要になります。  

* データの合計数が多すぎて、個別のデータベースの制約に適合しない
* ワークロード全体のトランザクションのスループットが個別のデータベースの能力を超えている
* テナントを互いに物理的に独立させる必要があり、テナントごとに別個のデータベースが必要になる
* コンプライアンス、パフォーマンス、または地政学上の理由から、データベースの異なる部分を異なる地理的場所に配置することが必要になる

他のシナリオ (たとえば、分散されたデバイスからのデータの取り込み) では、シャーディングを使用して、一時的に構成されたデータベースのセットに値を入力できます。 たとえば、別個のデータベースをそれぞれの日または週専用に使用できます。 その場合、シャーディング キーとして (シャード化テーブルのすべての行に存在する) 日付を表す整数を使用できます。アプリケーションは、日付の範囲の情報を取得するクエリを、対象の範囲をカバーするデータベースのサブセットにルーティングする必要があります。

シャーディングは、アプリケーションのすべてのトランザクションをシャーディング キーの単一の値に制限できる場合に最適です。 これにより、すべてのトランザクションが特定のデータベースにローカルであることが保証されます。

## <a name="multi-tenant-and-single-tenant"></a>マルチ テナントとシングル テナント

一部のアプリケーションでは、テナントごとに別個のデータベースを作成する最も単純なアプローチが使用されています。 このアプローチは、テナント単位で分離、バックアップ/復元、およびリソースのスケーリングを可能にする、**単一テナントのシャーディング パターン** です。 単一テナントのシャーディングでは、各データベースは特定のテナント ID 値 (または顧客キー値) に関連付けられますが、キーは必ずしもデータ自体に存在する必要はありません。 各要求を適切なデータベースにルーティングするのはアプリケーションの役目です。クライアント ライブラリはこの作業を簡素化できます。

![単一テナントをマルチテナント][4]

複数のテナントを別個のデータベースに分離する代わりに、一緒にデータベースにパックするシナリオもあります。 このパターンが典型的な **マルチテナントのシャーディング パターン** です。このパターンの利用は、1 つのアプリケーションが多数の小さなテナントを管理するという状況により促進されます。 マルチテナントのシャーディングでは、データベース テーブル内の行は、テナント ID を識別するキーまたはシャーディング キーを格納するように設計されます。 ここでも、アプリケーション層は、テナントの要求を適切なデータベースにルーティングする役割を負います。これは、エラスティック データベース クライアント ライブラリによってサポートされます。 さらに、行レベルのセキュリティを使用して、各テナントがアクセスできる行をフィルター処理することができます。詳細については、「[Elastic Database ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)」をご覧ください。 マルチ テナントのシャーディング パターンでは、データを複数のデータベース間で再分散することが必要な場合があります。この再分散は、エラスティック データベースの分割/マージ ツールで容易に行うことができます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)」を参照してください。

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>マルチテナントデータベースからシングルテナント データベースへのデータ移動
SaaS アプリケーションを作成する場合は、見込顧客に試用版のソフトウェアを提供するのが一般的です。 この場合は、データにマルチテナント データベースを使用すると費用対効果を高めることができます。 ただし、見込顧客が実際に顧客になった場合、シングルテナント データベースを使用した方がパフォーマンスが向上します。 このため、顧客が試用期間中にデータを作成していた場合には、 [分割/マージ ツール](elastic-scale-overview-split-and-merge.md) を使用して、マルチテナント データベースから新しいシングルテナント データベースにデータを移動します。

## <a name="next-steps"></a>次のステップ
クライアント ライブラリの使い方を示すサンプル アプリについては、「[Elastic Database ツールの概要](elastic-scale-get-started.md)」をご覧ください。

ツールを使用するように既存のデータベースを変換する方法については、「[既存のデータベースを移行してスケールアウト](elastic-convert-to-use-elastic-tools.md)」をご覧ください。

エラスティック プールの詳細を確認するには、「[エラスティック プールの価格およびパフォーマンスに関する考慮事項](elastic-pool-overview.md)」を参照するか、[エラスティック プール](elastic-pool-manage.md)で新しいプールを作成してください。  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png
