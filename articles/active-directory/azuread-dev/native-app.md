---
title: Azure Active Directory のネイティブ アプリ
description: ネイティブ アプリとは何かと、この種のアプリのプロトコル フロー、登録、およびトークンの有効期限の基本について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154799"
---
# <a name="native-apps"></a>ネイティブ アプリ

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

ネイティブ アプリとは、ユーザーのために Web API を呼び出すアプリケーションです。 このシナリオは、[OAuth 2.0 仕様](https://tools.ietf.org/html/rfc6749)のセクション 4.1 に記載されている、パブリック クライアントを使用した OAuth 2.0 認証コード付与に基づいています。 ネイティブ アプリケーションが、OAuth 2.0 プロトコルを使用してユーザーのアクセス トークンを取得します。 このアクセス トークンが要求で Web API に送信されます。Web API がユーザーを認証し、目的のリソースを返します。

## <a name="diagram"></a>ダイアグラム

![ネイティブ アプリケーション対 Web API のダイアグラム](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>プロトコル フロー

AD 認証ライブラリを使用している場合は、ブラウザー ポップアップ、トークンのキャッシュ、更新トークンの処理など、以下で説明するほとんどのプロトコルの詳細が自動的に処理されます。

1. ブラウザー ポップアップを使用して、ネイティブ アプリケーションが Azure AD の認証エンドポイントに要求を送信します。 この要求には、Azure Portal に表示されるネイティブ アプリケーションのアプリケーション ID とリダイレクト URI、および Web API のアプリケーション ID の URI が含まれています。 ユーザーがまだサインインしていない場合は、再度サインインするよう求められます
1. Azure AD がユーザーを認証します。 これがマルチテナント アプリケーションであり、アプリケーションを使用するために同意が必要な場合、まだ同意していないユーザーは同意を求められます。 同意後、認証が成功すると、Azure AD により認証コード応答が発行されて、クライアント アプリケーションのリダイレクト URI に送信されます。
1. Azure AD が認証コード応答を発行してリダイレクト URI に送信すると、クライアント アプリケーションはブラウザーの対話を停止し、応答から認証コードを抽出します。 この認証コードを使用して、クライアント アプリケーションは、認証コード、クライアント アプリケーションの詳細 (アプリケーション ID とリダイレクト URI)、目的のリソース (Web API のアプリケーション ID の URI) を要求に含めて Azure AD のトークン エンドポイントに送信します。
1. 認証コードおよびクライアント アプリケーションと Web API に関する情報が Azure AD によって検証されます。 検証が正常に行われると、Azure AD は JWT アクセス トークンと JWT 更新トークンの 2 つのトークンを返します。 また、ユーザーに関する基本情報 (表示名やテナント ID など) も返します。
1. クライアント アプリケーションは、返された JWT アクセス トークンを HTTPS 経由で使用して、Web API への要求の Authorization ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。
1. アクセス トークンの有効期限が切れると、クライアント アプリケーションは、ユーザーを再度認証する必要があることを示すエラーを受信します。 アプリケーションが有効な更新トークンを保持している場合は、ユーザーに再度サインインを求めずに、その更新トークンを使用して新しいアクセス トークンを取得できます。 更新トークンの有効期限が切れた場合、アプリケーションは対話形式でユーザーをもう一度認証する必要があります。

> [!NOTE]
> Azure AD によって発行された更新トークンは、複数のリソースへのアクセスに使用できます。 たとえば、2 つの Web API を呼び出す権限を持つクライアント アプリケーションがある場合、更新トークンを使用して、もう 1 つの Web API へのアクセス トークンも取得できます。

## <a name="code-samples"></a>コード サンプル

ネイティブ アプリケーション対 Web API のシナリオのコード サンプルをご覧ください。 新しいサンプルが頻繁に追加されているので、頻繁に確認してください。 [ネイティブ アプリケーション対 Web API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)

## <a name="app-registration"></a>アプリの登録

Azure AD v1.0 エンドポイントにアプリケーションを登録するには、[アプリの登録](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)に関するページをご覧ください。

* シングル テナント - ネイティブ アプリケーションと Web API の両方を、Azure AD の同じディレクトリに登録する必要があります。 ネイティブ アプリケーションによるリソースへのアクセスを制限するために使用する一連のアクセス許可を公開するように Web API を構成できます。 その後、クライアント アプリケーションで、Azure portal の [他のアプリケーションに対するアクセス許可] ドロップダウン メニューから目的のアクセス許可を選択します。
* マルチテナント - ネイティブ アプリケーションは、これまで開発者または発行者のディレクトリにのみ登録されていました。 アプリケーションが機能するために必要なアクセス許可を示すようにネイティブ アプリケーションを構成します。 必要なアクセス許可のこのリストは、アプリケーションを組織で使用できるように、発行先ディレクトリ内のユーザーまたは管理者がアプリケーションに同意するときにダイアログに表示されます。 組織内の任意のユーザーが同意できる、ユーザーレベルのアクセス許可だけを必要とするアプリケーションもあれば、 組織内のユーザーは同意できない、管理者レベルのアクセス許可を必要とするアプリケーションもあります。 このレベルのアクセスを必要とするアプリケーションに同意できるのはディレクトリ管理者だけです。 ユーザーまたは管理者が同意すると、Web API だけがディレクトリに登録されます。 

## <a name="token-expiration"></a>トークンの有効期限

ネイティブ アプリケーションは、認証コードを使用して JWT アクセス トークンを取得するときに、JWT 更新トークンも受信します。 アクセス トークンの有効期限が切れたときは、ユーザーに再度サインインを要求しなくても、更新トークンを使用してユーザーを再認証できます。 この更新トークンがユーザーの認証に使用されると、新しいアクセス トークンと更新トークンが取得されます。

## <a name="next-steps"></a>次のステップ

- その他の[アプリケーションの種類とシナリオ](app-types.md)について学習する
- Azure AD [認証の基本](v1-authentication-scenarios.md)について学習する
