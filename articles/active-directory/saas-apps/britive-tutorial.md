---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Britive の統合 | Microsoft Docs
description: Azure Active Directory と Britive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2020
ms.author: jeedes
ms.openlocfilehash: 74f449afe2081feecfe7f0194b20429314ec572d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Britive の統合

このチュートリアルでは、Britive と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Britive を統合すると、次のことができます。

* Britive にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Britive に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Britive サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Britive では、**SP** Initiated SSO がサポートされます
* Britive を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-britive-from-the-gallery"></a>ギャラリーからの Britive の追加

Azure AD への Britive の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Britive を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Britive**」と入力します。
1. 結果のパネルから **[Britive]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-britive"></a>Britive の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Britive に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Britive の関連ユーザーの間にリンク関係を確立する必要があります。

Britive に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Britive の SSO の構成](#configure-britive-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Britive のテスト ユーザーの作成](#create-britive-test-user)** - Britive で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Britive** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<TENANTNAME>.britive-app.com/sso`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`urn:amazon:cognito:sp:<UNIQUE_ID>`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL と識別子で更新します。実際の値については後で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Britive のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Britive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Britive]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-britive-sso"></a>Britive の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Britive Web サイトにサインインします。

1. **管理者設定アイコン** をクリックし、 **[Security]\(セキュリティ\)** を選択します。

    ![このスクリーンショットは、[Settings]\(設定\) と [Security]\(セキュリティ\) が選択された状態の Britive Web サイトを示しています。](./media/britive-tutorial/configure1.png)

1. **[SSO Configuration]\(SSO 構成\)** を選択し、次の手順を実行します。

    ![このスクリーンショットは、[S S O Configuration]\(S S O 構成\) を示しています。ここで、この手順の情報を入力します。](./media/britive-tutorial/configure2.png)

    a. **[Audience/Entity ID]\(対象ユーザーおよびエンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** テキスト ボックスに貼り付けます。

    b. **[Initiate SSO URL]\(SSO の開始 URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** テキスト ボックスに貼り付けます。

    c. **[UPLOAD SAML METADATA]\(SAML メタデータのアップロード\)** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードします。 メタデータ ファイルをアップロードすると、上記の値が自動的に設定され、変更が保存されます。

### <a name="create-britive-test-user"></a>Britive のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として Britive Web サイトにサインインします。

1. **管理者設定アイコン** をクリックし、 **[User Administration]\(ユーザーの管理\)** を選択します。

    ![このスクリーンショットは、[Settings]\(設定\) と [User Administration]\(ユーザーの管理\) が選択された状態の Britive Web サイトを示しています。](./media/britive-tutorial/user1.png)

1. **[ADD USER]\(ユーザーの追加)** をクリックします。

    ![このスクリーンショットは、[ADD USER]\(ユーザーの追加) ボタンを示しています。](./media/britive-tutorial/user2.png)

1. 組織の要件に従って、ユーザーに関する必要な詳細情報をすべて入力し、 **[ADD USER]\(ユーザーの追加)** をクリックします。

    ![このスクリーンショットは、[Ad a User]\(ユーザーの追加\) ページを示しています。ここで、ユーザー情報を入力します。](./media/britive-tutorial/user3.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Britive] タイルをクリックすると、SSO を設定した Britive に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Britive を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)