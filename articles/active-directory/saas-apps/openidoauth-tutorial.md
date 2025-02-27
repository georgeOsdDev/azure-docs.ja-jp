---
title: Azure AD アプリ ギャラリーの OpenID および OAuth アプリケーションを構成する | Microsoft Docs
description: Azure AD アプリ ギャラリーの OpenID および OAuth アプリケーションを構成する手順。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 32f79f24df6fe705146b39750c710450ef8f1f7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735941"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーの OpenID および OAuth アプリケーションを構成する

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>ギャラリーから OpenID アプリケーションを追加する手順

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. ダイアログの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに、アプリケーション名を入力します。 検索パネルで目的のアプリケーションを選択して、アプリケーションにサインアップします。

    ![結果一覧の Openid](common/search-new-app.png)


1. [アプリケーション名] ページで **[サインアップ]** ボタンをクリックします。

    ![[追加] ボタン](./media/openidoauth-tutorial/addbutton.png)

    > [!NOTE]
    > ここでテナント管理者は、サインアップ ボタンを選択して、アプリケーションに同意する必要があります。 これでアプリケーションが顧客テナントに追加され、必要な構成を行うことができます。 アプリケーションを明示的に追加する必要はありません。

5. サインイン資格情報のために、アプリケーションのログイン ページまたは Azure Active Directory (Azure AD) ページにリダイレクトされます。

6. 認証に成功したら、同意ページで同意を受け入れます。 その後、アプリケーションのホーム ページが表示されます。

    > [!NOTE]
    > 追加できるアプリケーションのインスタンスは 1 つだけです。 既に 1 つ追加しているときに再び同意を試みたとしても、再度テナントに追加されることはありません。 そのため論理的には、テナント内で使用できるのは 1 つのアプリ インスタンスのみです。

1. 下のビデオに従って、ギャラリーから OpenID アプリケーションを追加します。
    >[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="authentication-flow-using-openid-connect"></a>OpenID Connect を使用する認証フロー

最も基本的なサインイン フローには次の手順が含まれています。

![OpenID Connect を使用する認証フロー](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>マルチテナント アプリケーション
マルチテナント アプリケーションは、1 つの組織ではなく、多数の組織で使用することを目的としています。 通常、これらは独立系ソフトウェア ベンダー (ISV) によって作成された SaaS (サービスとしてのソフトウェア) アプリケーションです。

マルチテナント アプリケーションは、それらが使用される各ディレクトリにプロビジョニングする必要があります。 これらを登録するには、ユーザーまたは管理者の同意が必要です。 この同意プロセスは、アプリケーションをディレクトリに登録し、Graph API または別の Web API へのアクセス権を付与するときに開始されます。 別の組織のユーザーまたは管理者がアプリケーションを使用するためにサインアップすると、アプリケーションに必要なアクセス許可がダイアログ ボックスに表示されます。

ユーザーまたは管理者は、そこでアプリケーションに同意することができます。 この同意によって、アプリケーションは定められたデータへのアクセスが許可され、最終的にディレクトリに登録されます。

> [!NOTE]
> 複数のディレクトリ内のユーザーがアプリケーションを使用できるようにする場合、ユーザーが属するテナントを確認するためのメカニズムが必要です。 シングルテナント アプリケーションでは、それ自体のディレクトリでユーザーを探すだけで済みます。 マルチテナント アプリケーションでは、Azure AD のすべてのディレクトリから特定のユーザーを識別する必要があります。
>
> このタスクを実行するために、Azure AD には、テナント固有のエンドポイントの代わりに、マルチテナント アプリケーションがサインイン要求を送信できる共通の認証エンドポイントが用意されています。 このエンドポイントは Azure AD のすべてのディレクトリで `https://login.microsoftonline.com/common` です。 テナント固有のエンドポイントであれば `https://login.microsoftonline.com/contoso.onmicrosoft.com` のようになります。
>
> アプリケーションを開発するときは、共通のエンドポイントを考慮することが重要です。 サインイン、サインアウト、トークンの検証時に複数のテナントに対応するためのロジックが必要となります。

Azure AD では、既定でマルチテナント アプリケーションが奨励されます。 これらは、複数の組織全体でアクセスが容易です。また、お客様が同意を受け入れた後の使用が容易です。

## <a name="consent-framework"></a>同意フレームワーク

Azure AD の同意フレームワークを使用して、マルチテナントの Web クライアント アプリケーションとネイティブ クライアント アプリケーションを開発できます。 こうしたアプリケーションには、登録されている Azure AD テナントとは異なるテナントのユーザー アカウントを使ってサインインできます。 また、次のような Web API へのアクセスが必要になることもあります。
- Microsoft Graph API (Azure AD、Intune、Microsoft 365 のサービスにアクセスするため)。
- その他の Microsoft サービスの API。
- お客様独自の Web API。

このフレームワークは、ディレクトリへの登録を要求するアプリケーションに対して同意を与えるユーザーまたは管理者の存在が前提となっています。 登録には、ディレクトリ データへのアクセスが伴う場合があります。 同意が与えられると、クライアント アプリケーションがユーザーに代わって Microsoft Graph API を呼び出し、必要に応じて情報を利用できるようになります。

[Microsoft Graph API](https://developer.microsoft.com/graph/) を使用してアクセスできる Microsoft 365 のデータの例を次に示します。

- Exchange の予定表とメッセージ。
- SharePoint のサイトとリスト。
- OneDrive のドキュメント。
- OneNote のノートブック。
- Planner のタスク。
- Excel のブック。

Azure AD のユーザーとグループや、Microsoft クラウド サービスの他のデータ オブジェクトにも、Graph API を使用してアクセスすることができます。

以下の手順は、アプリケーションの開発者とユーザーにとっての同意エクスペリエンスがどのようなものになるかを示しています。

1. リソースまたは API にアクセスするために一定のアクセス許可を要求する必要がある Web クライアント アプリケーションがあると仮定しましょう。 Azure Portal は、構成時にアクセス許可要求を宣言するために使用されます。 これらは他の構成設定と同様、アプリケーションを Azure AD に登録する一環として行います。 必要なアクセス許可の要求パスについては、以下の手順に従ってください。

    a. メニューの左側で **[アプリの登録]** をクリックし、検索ボックスにアプリケーション名を入力して自分のアプリケーションを開きます。

    ![左側のメニューから [アプリの登録] が選択されている画面のスクリーンショット。[アプリケーション ID] 検索ボックスが強調表示されています。](./media/openidoauth-tutorial/application.png)

    b. **[API アクセス許可の表示]** をクリックします。

    ![[Call API]\(API 呼び出し\) ページのスクリーンショット。[API アクセス許可の表示] ボタンが選択されています。](./media/openidoauth-tutorial/api-permission.png)

    c. **[アクセス許可の追加]** をクリックします。

    ![[API のアクセス許可] セクションのスクリーンショット。[アクセス許可の追加] ボタンが選択されています。](./media/openidoauth-tutorial/add-permission.png)

    d. **[Microsoft Graph]** をクリックします。

    ![[API アクセス許可の要求] ページのスクリーンショット。[Microsoft API] タブと [Microsoft Graph] タイルが選択されています。](./media/openidoauth-tutorial/microsoft-graph.png)

    e. **[委任されたアクセス許可]** と **[アプリケーションのアクセス許可]** から必要なオプションを選択します。

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. アプリケーションのアクセス許可が更新された状況を考えてみましょう。 アプリケーションは実行中であり、ユーザーが初めてアプリケーションを使うところです。 アプリケーションではまず、Azure AD の /authorize エンドポイントから認証コードを取得する必要があります。 取得した認証コードは、後でアクセス トークンと更新トークンの取得に使用します。

3. ユーザーの認証がまだであれば、Azure AD の /authorize エンドポイントによってサインイン画面が表示されます。

    ![アカウントのサインイン プロンプトのスクリーンショット](./media/openidoauth-tutorial/authentication.png)

4. ユーザーのサインインが終わると、そのユーザーに対して同意ページを表示する必要があるかどうかが Azure AD により判定されます。 表示の要否の判定基準は、ユーザー (またはそのユーザーが所属する組織の管理者) がアプリケーションに既に同意を与えているかどうかです。

   同意がまだであれば、Azure AD からユーザーに対して同意を求めるメッセージと、アプリケーションが機能するうえで必要なアクセス許可が表示されます。 同意ダイアログ ボックスに表示されるアクセス許可は、Azure portal の [委任されたアクセス許可] で選択されているものと同じになります。

    ![同意ページ](./media/openidoauth-tutorial/consentpage.png)

通常のユーザーはいくつかのアクセス許可に同意できます。 その他のアクセス許可では、テナント管理者の同意が必要になります。

## <a name="difference-between-admin-consent-and-user-consent"></a>管理者の同意とユーザーの同意の違い

管理者であれば、テナント内のユーザー全員に代わってアプリケーションの委任されたアクセス許可に同意することもできます。 管理者が同意すると、テナントの各ユーザーには同意ダイアログ ボックスが表示されなくなります。 管理者ロールを持つユーザーは Azure portal で同意を付与することができます。 アプリケーションの **[設定]** ページで、 **[必要なアクセス許可]**  >  **[管理者の同意の付与]** の順に選択します。

![[アクセス許可の付与] ボタン](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> 現在、ADAL.js が使用されるシングルページ アプリケーション (SPA) では、 **[管理者の同意の付与]** ボタンを使用して明示的に同意を付与する必要があります。 そうしないと、アクセス トークンが要求されたときにアプリケーションでエラーが発生します。

アプリケーション専用アクセス許可では、常にテナント管理者の同意が必要になります。 アプリケーションがアプリ専用アクセス許可を要求する場合に、ユーザーがそのアプリケーションにサインインしようとすると、エラー メッセージが表示されます。 メッセージによって、そのユーザーは同意できないことが伝えられます。

アプリケーションで管理者の同意が必要なアクセス許可を使用する場合、ジェスチャ (管理者がアクションを開始できるボタンやリンク) を設定する必要があります。 通常、このアクションに対してアプリケーションから送信される要求は OAuth2 または OpenID Connect 承認要求です。 この要求には *prompt=admin_consent* というクエリ文字列パラメーターが含まれています。

管理者が同意し、顧客のテナントにサービス プリンシパルが作成されると、以降のサインイン要求では *prompt=admin_consent* パラメーターは不要になります。 管理者は要求されたアクセス許可を許容可能と判断しているため、その時点からは、テナント内の他のユーザーが同意を求められることはありません。

テナント管理者は、通常ユーザーによるアプリケーションへの同意を無効にすることができます。 通常ユーザーによる同意が無効化された場合、テナントでアプリケーションを使用するには常に管理者の同意が必要になります。 エンド ユーザーによる同意を無効化した状態でアプリケーションをテストしたい場合は、[Azure portal](https://portal.azure.com/) に構成スイッチがあります。 これは **[エンタープライズ アプリケーション]** の [[ユーザー設定]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) セクションにあります。

*prompt=admin_consent* パラメーターは、管理者の同意を必要としないアクセス許可を要求するアプリケーションでも使用できます。 たとえば、テナント管理者が 1 回 "サインアップする" と、その時点からは他のユーザーが同意を求められないエクスペリエンスを必要とするアプリケーションが該当します。

アプリケーションが管理者の同意を必要とし、*prompt=admin_consent* パラメーターが送信されることなく、管理者がサインインすることを想像してみてください。 管理者がアプリケーションへの同意に成功したとき、それが適用されるのは、自分のユーザー アカウントだけです。 通常のユーザーは、アプリケーションへのサインインも同意も実行できないままです。 この機能は、他のユーザーのアクセスを許可する前に、テナント管理者がアプリケーションを確認できるようにしたい場合に役立ちます。

## <a name="next-steps"></a>次の手順

[Azure Active Directory (Azure AD) テナント内のアプリケーションへの OIDC ベースのシングル サインオン (SSO) を設定する](../manage-apps/add-application-portal-setup-oidc-sso.md)