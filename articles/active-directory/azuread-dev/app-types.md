---
title: v1.0 でのアプリケーションの種類 | Azure
description: Azure Active Directory v2.0 エンドポイントでサポートされているアプリの種類とシナリオについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88116446"
---
# <a name="application-types-in-v10"></a>v1.0 でのアプリケーションの種類

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) は、さまざまな最新アプリ アーキテクチャ向けの認証をサポートしています。そのいずれも、業界標準のプロトコルである OAuth 2.0 または OpenID Connect に基づいています。

次の図は、これらのシナリオとアプリケーションの種類、およびさまざまなコンポーネントを追加する方法を示しています。

![アプリケーションの種類とシナリオ](./media/authentication-scenarios/application-types-scenarios.png)

Azure AD でサポートされる 5 つの主要なアプリケーション シナリオは、次のとおりです。

- **[シングル ページ アプリケーション (SPA)](single-page-application.md)**: ユーザーは、Azure AD によって保護されたシングル ページ アプリケーションにサインインする必要があります。
- **[Web ブラウザー対 Web アプリケーション](web-app.md)** : ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります。
- **[ネイティブ アプリケーション対 Web API](native-app.md)** : スマートフォン、タブレット、または PC で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するために、ユーザーを認証する必要があります。
- **[Web アプリケーション対 Web API](web-api.md)** : Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。
- **[デーモンまたはサーバー アプリケーション対 Web API](service-to-service.md)** : Web ユーザー インターフェイスを備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。

コードの作業を始める前に、各アプリの種類の詳細を学習し、シナリオの概要を理解するには、リンクに従ってください。 v1.0 エンドポイントまたは v2.0 エンドポイントで動作する特定のアプリの作成時に知っておく必要がある相違点について学習することもできます。

> [!NOTE]
> Azure AD のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json)に関する記事を参照してください。

さまざまな言語とプラットフォームを使用して、ここで説明されているすべてのアプリとシナリオを開発できます。 すべてはコード サンプル ガイド ([シナリオ別 v1.0 コード サンプル](sample-v1-code.md)および[シナリオ別 v2.0 コード サンプル](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)) で使用可能な完全なコード サンプルで裏付けられています。 また、対応する [GitHub サンプル リポジトリ](https://github.com/Azure-Samples?q=active-directory)からコード サンプルを直接ダウンロードすることもできます。

また、アプリケーションでエンド ツー エンドのシナリオの特定の部分やセグメントが必要なときには、ほとんどの場合、その機能を個別に追加できます。 たとえば、Web API を呼び出すネイティブ アプリケーションがある場合、その Web API も呼び出す Web アプリケーションを簡単に追加できます。

## <a name="app-registration"></a>アプリの登録

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Azure AD v1.0 エンドポイントを使用するアプリの登録

Azure AD に認証を委託するすべてのアプリケーションを、ディレクトリに登録する必要があります。 この手順では、アプリケーションが配置されている URL、認証後の応答の送信先の URL、アプリケーションを識別する URI など、アプリケーションに関する情報を Azure AD に伝える必要があります。 この情報が必要となる主な理由は次のとおりです。

* Azure AD は、サインオンの処理時やトークンの交換時にアプリケーションと通信する必要があります。 Azure AD とアプリケーションの間では、次のような情報が渡されます。
  
  * **アプリケーション ID の URI** - アプリケーションの識別子です。 この値は、呼び出し元がどのアプリケーションのトークンを要求しているのかを示すために、認証時に Azure AD に送信されます。 また、この値は、意図したターゲットであったことをアプリケーションがわかるようにトークンにも含まれます。
  * **応答 URL** と **リダイレクト URI** - Web API または Web アプリケーションの場合、応答 URL は Azure AD が認証の応答 (認証が成功した場合のトークンなど) を送信する場所です。 ネイティブ アプリケーションの場合、リダイレクト URI は、Azure AD が OAuth 2.0 要求でユーザー エージェントをリダイレクトする宛先の一意の識別子です。
  * **アプリケーション ID** - アプリケーションの登録時に Azure AD によって生成されるアプリケーションの ID です。 承認コードまたはトークンを要求すると、認証時にアプリケーション ID とキーが Azure AD に送信されます。
  * **キー** - Web API を呼び出すために Azure AD に対して認証するときに、アプリケーション ID と共に送信されるキーです。
* Azure AD は、アプリケーションに、ディレクトリ データや組織の他のアプリケーションなどにアクセスするために必要な権限があることを保証する必要があります。

詳細については、[アプリの登録](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)方法を参照してください。

## <a name="single-tenant-and-multi-tenant-apps"></a>単一テナント アプリとマルチテナント アプリ

開発し、Azure AD と統合できるアプリケーションには 2 つのカテゴリがあることを理解しておくと、プロビジョニングが明確になります。

* **シングル テナント アプリケーション** - シングル テナント アプリケーションは、1 つの組織で使用することを目的としています。 通常、これらはエンタープライズ開発者によって作成された基幹業務 (LOB) アプリケーションです。 シングル テナント アプリケーションには、1 つのディレクトリ内のユーザーだけがアクセスできればよいため、1 つのディレクトリにプロビジョニングするだけで済みます。 一般に、これらのアプリケーションは組織の開発者が登録します。
* **マルチテナント アプリケーション** - マルチテナント アプリケーションは、1 つの組織ではなく、多数の組織で使用することを目的としています。 通常、これらは独立系ソフトウェア ベンダー (ISV) によって作成された SaaS (サービスとしてのソフトウェア) アプリケーションです。 マルチテナント アプリケーションは、アプリケーションが使用される各ディレクトリにプロビジョニングする必要があります。アプリケーションを登録するには、ユーザーまたは管理者の同意が必要です。 この同意プロセスは、アプリケーションをディレクトリに登録し、Graph API または別の Web API へのアクセス権を付与するときに開始されます。 別の組織のユーザーまたは管理者がアプリケーションを使用するためにサインアップすると、アプリケーションに必要なアクセス許可を示すダイアログが表示されます。 ユーザーまたは管理者がアプリケーションに同意すると、アプリケーションは定められたデータへのアクセスが許可され、最終的にディレクトリに登録されます。 詳細については、「[同意フレームワークの概要](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)」をご覧ください。

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>シングル テナント アプリまたはマルチテナント アプリを開発する場合のその他の考慮事項

シングル テナント アプリケーションではなく、マルチテナント アプリケーションを開発する場合は考慮事項が増えます。 たとえば、複数のディレクトリ内のユーザーがアプリケーションを使用できるようにする場合、ユーザーが属するテナントを特定するメカニズムが必要です。 シングル テナント アプリケーションでは、独自のディレクトリでユーザーを探すだけで済みますが、マルチテナント アプリケーションでは、Azure AD のすべてのディレクトリから特定のユーザーを識別する必要があります。 このタスクを実行するために、Azure AD には、テナント固有のエンドポイントの代わりに、マルチテナント アプリケーションがサインイン要求を送信できる共通の認証エンドポイントが用意されています。 このエンドポイントは Azure AD のすべてのディレクトリで `https://login.microsoftonline.com/common` ですが、テナント固有のエンドポイントは `https://login.microsoftonline.com/contoso.onmicrosoft.com` の場合があります。 サインイン、サインアウト、およびトークンの検証時に複数のテナントに対応するためのロジックが必要となるため、アプリケーションを開発するときは、共通のエンドポイントを考慮することが特に重要です。

シングル テナント アプリケーションを現在開発中であり、そのアプリケーションを多数の組織で使用できるようにしたい場合は、マルチテナントに対応できるように、アプリケーションとその構成を Azure AD で簡単に変更できます。 また、Azure AD では、シングル テナント アプリケーションとマルチテナント アプリケーションのどちらで認証を提供するかに関係なく、すべてのディレクトリですべてのトークンに同じ署名キーを使用します。

このドキュメントに記載されている各シナリオには、プロビジョニングの要件を説明するサブセクションが含まれています。 Azure AD でのアプリケーションのプロビジョニング、およびシングル テナント アプリケーションとマルチテナント アプリケーションの違いの詳細については、「[Azure Active Directory とアプリケーションの統合](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)」を参照してください。 Azure AD の共通のアプリケーション シナリオを理解するために、このドキュメントを引き続きお読みください。

## <a name="next-steps"></a>次のステップ

- Azure AD での他の[認証の基礎](v1-authentication-scenarios.md)について詳細を参照する。