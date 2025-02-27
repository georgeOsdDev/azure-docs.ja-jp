---
title: セルフサービス パスワード リセットをトラブルシューティングする - Azure Active Directory
description: Azure Active Directory でのセルフサービス パスワード リセットに関する一般的な問題をトラブルシューティングする方法と解決手順について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600864"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Azure Active Directory でセルフサービス パスワード リセットをトラブルシューティングする

Azure Active Directory (Azure AD) のセルフサービス パスワード リセット (SSPR) を使用すると、ユーザーはクラウド内の自分のパスワードをリセットできます。

SSPR で問題が発生した場合は、次のトラブルシューティング手順と一般的なエラーが役立つことがあります。 この短いビデオでは、[6 つの最も一般的な SSPR エンドユーザー エラー メッセージを解決する方法](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1)についても説明します。

問題に対する回答が見つからない場合は、[Microsoft のサポート チームがいつでも](#contact-microsoft-support)問題の解決をお手伝いいたします。

## <a name="sspr-configuration-in-the-azure-portal"></a>Azure Portal の SSPR 構成

Azure portal での SSPR オプションの表示または構成で問題が発生した場合は、次のトラブルシューティングの手順を確認してください。

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Azure Portal で Azure AD の下に **[パスワードのリセット]** セクションが表示されません。

操作を実行する管理者に Azure AD ライセンスが割り当てられていない場合、 **[パスワードのリセット]** メニュー オプションは表示されません。

対象の管理者アカウントにライセンスを割り当てるには、[ライセンスの割り当て、検証、および問題の解決](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事の手順に従います。

### <a name="i-dont-see-a-particular-configuration-option"></a>特定の構成オプションが表示されません。

UI の多くの要素は、必要になるまで表示されません。 特定の構成オプションを探す前に、オプションが有効になっていることを確認してください。

### <a name="i-dont-see-the-on-premises-integration-tab"></a>**[オンプレミスの統合]** タブが表示されません。

オンプレミスのパスワード ライトバックは、Azure AD Connect をダウンロードして機能を構成した場合にのみ表示されます。

詳細については、[Azure AD Connect の概要](../hybrid/how-to-connect-install-express.md)に関する記事を参照してください。

## <a name="sspr-reporting"></a>SSPR レポート

Azure portal の SSPR レポートで問題が発生した場合は、次のトラブルシューティングの手順を確認してください。

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>**[セルフサービスのパスワード管理]** 監査イベント カテゴリに、パスワード管理アクティビティの種類が表示されません。

これは、この操作を実行する管理者に割り当てられる Azure AD ライセンスを持っていない場合に発生します。

対象の管理者アカウントにライセンスを割り当てるには、[ライセンスの割り当て、検証、および問題の解決](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事の手順に従います。

### <a name="user-registrations-show-multiple-times"></a>ユーザー登録が何回も表示されます。

ユーザーが登録を行うと、現時点では、登録される各データを個別のイベントとして記録しています。

このデータを集計してデータの表示方法をより柔軟なものにするには、レポートをダウンロードして Excel のピボット テーブルでデータを開きます。

## <a name="sspr-registration-portal"></a>SSPR 登録ポータル

ユーザーが SSPR の登録を行うときに問題が発生した場合は、次のトラブルシューティングの手順を確認してください。

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>このディレクトリでは、パスワードのリセットが有効になっていません。 "管理者がこの機能を使用することを許可していません" というエラーがユーザーに表示されることがあります。

SSPR は、すべてのユーザーまたは選択したグループのユーザーに対して有効にするか、すべてのユーザーに対して無効にすることができます。 現在、Azure portal を使用して SSPR に対して有効にできる Azure AD グループは 1 つだけです。 SSPR のより広範な展開の一環として、入れ子になったグループがサポートされています。 選択したグループ内のユーザーに適切なライセンスが割り当てられていることを確認してください。

Azure portal で、 **[セルフサービス パスワード リセットが有効]** の構成を *[選択]* または *[すべて]* に変更して、 **[保存]** を選択します。

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>ユーザーに Azure AD のライセンスが割り当てられていません。 "管理者がこの機能を使用することを許可していません" というエラーがユーザーに表示されることがあります。

現在、Azure portal を使用して SSPR に対して有効にできる Azure AD グループは 1 つだけです。 SSPR のより広範な展開の一環として、入れ子になったグループがサポートされています。 選択したグループ内のユーザーに適切なライセンスが割り当てられていることを確認してください。 前のトラブルシューティングの手順を確認し、必要に応じて SSPR を有効にします。

また、トラブルシューティングの手順を確認して、構成オプションの設定を実行している管理者にライセンスが割り当てられていることを確認します。 対象の管理者アカウントにライセンスを割り当てるには、[ライセンスの割り当て、検証、および問題の解決](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事の手順に従います。

### <a name="theres-an-error-processing-the-request"></a>要求の処理中にエラーが発生しました。

一般的な SSPR 登録エラーはさまざまな問題が原因で発生することがありますが、一般的にサービスの停止や構成の問題が原因で発生します。 SSPR 登録処理を再試行したときに、この一般的なエラーが引き続き表示される場合は、[Microsoft サポートに問い合わせて](#contact-microsoft-support)、追加のサポートを要請してください。

## <a name="sspr-usage"></a>SSPR の使用

管理者またはユーザーが SSPR を使用しているときに問題が発生した場合は、次のトラブルシューティングのシナリオと解決手順を確認してください。

| エラー | 解決策 |
| --- | --- |
| このディレクトリでは、パスワードのリセットが有効になっていません。 | Azure portal で、 **[セルフサービス パスワード リセットが有効]** の構成を *[選択]* または *[すべて]* に変更して、 **[保存]** を選択します。 |
| ユーザーに Azure AD のライセンスが割り当てられていません。 | これは、対象のユーザーに Azure AD ライセンスが割り当てられていない場合に発生することがあります。 対象の管理者アカウントにライセンスを割り当てるには、[ライセンスの割り当て、検証、および問題の解決](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事の手順に従います。 |
| ディレクトリでパスワードのリセットが有効になっていますが、ユーザーの認証情報が見つからない、または認証情報の形式が正しくありません。 | ユーザーがディレクトリ内のファイルに正しい形式の連絡先データを指定していることを確認します。 詳しくは、[Azure AD のセルフサービス パスワード リセット で使われるデータ](howto-sspr-authenticationdata.md)に関するページをご覧ください。 |
| ディレクトリでパスワードのリセットが有効になっていますが、ポリシーが 2 つの検証方法を要求するように設定されている場合に、ユーザーがファイルに 1 つの連絡先データしか指定していません。 | ユーザーが少なくとも 2 つの連絡方法を正しく構成していることを確認します。 たとえば、携帯電話番号 *と* 会社の電話番号などです。 |
| ディレクトリでパスワードのリセットが有効になっており、ユーザーが正しく構成されていますが、ユーザーに連絡できません。 | これは、一時的なサービス エラーが発生したか、連絡先データが間違っていて正しく検出できなかった可能性があります。 <br> <br> ユーザーが 10 秒間待機すると、[もう一度お試しください] と [管理者に問い合わせてください] のリンクが表示されます。 [もう一度お試しください] を選択した場合は、呼び出しが再試行されます。 ユーザーが [管理者に連絡してください] を選択すると、そのユーザーのアカウントに対してパスワードのリセットを実行するよう管理者に要求するフォーム メールが送信されます。 |
| ユーザーがパスワードのリセットの SMS または電話を受け取っていません。 | ディレクトリ内の電話番号の形式が正しくない可能性があります。 電話番号の形式が "+1 4251234567" となっていることをご確認ください。 <br> <br>ディレクトリ内の電話番号を指定した場合でも、パスワードのリセットは内線番号をサポートしていません。 内線番号は呼び出しが行われる前に削除されます。 内線番号のない電話番号を使用するか、構内交換機 (PBX) で電話番号と内線番号を統合してください。 |
| ユーザーがパスワードのリセットのメールを受け取っていません。 | この問題の主な原因は、メッセージがスパム フィルターによって拒否されたことである可能性があります。 スパム、迷惑メール、削除済みアイテムのフォルダーをご確認ください。 <br> <br> また、SSPR に登録されている正しい電子メール アカウントを確認するようにユーザーに要請してください。 |
| パスワードのリセット ポリシーを設定しましたが、管理者アカウントでパスワードのリセットを使用してもポリシーが適用されません。 | マイクロソフトは、最高レベルのセキュリティを維持するために、管理者パスワード リセット ポリシーを管理、制御しています。 |
| ユーザーが 1 日に何回もパスワードの　リセットを試みることはできません。 | ユーザーが短時間に何回もパスワードをリセットできないように、自動調整メカニズムが使用されています。 調整は次のシナリオで行われます。 <br><ul><li>ユーザーが 1 時間に 5 回、電話番号の検証を試みる場合。</li><li>ユーザーが 1 時間に 5 回、セキュリティの質問ゲートの使用を試みる場合。</li><li>ユーザーが 1 時間に 5 回、同じユーザー アカウントのパスワードのリセットを試みる場合。</li></ul>この問題が発生した場合、ユーザーは最後に試行したときから 24 時間待つ必要があります。 そのあと、ユーザーはパスワードをリセットできるようになります。 |
| ユーザーが自分の電話番号を検証するときにエラーが表示されます。 | このエラーは、入力した電話番号がファイルの電話番号と一致しない場合に発生します。 電話ベースの方法でパスワードのリセットを試みる場合は、ユーザーが国番号と市外局番を含む完全な電話番号を入力していることを確認します。 |
| 要求の処理中にエラーが発生しました。 | 一般的な SSPR 登録エラーはさまざまな問題が原因で発生することがありますが、一般的にサービスの停止や構成の問題が原因で発生します。 SSPR 登録処理を再試行したときに、この一般的なエラーが引き続き表示される場合は、[Microsoft サポートに問い合わせて](#contact-microsoft-support)、追加のサポートを要請してください。 |
| オンプレミスのポリシーの違反 | パスワードは、オンプレミスの Active Directory のパスワード ポリシーを満たしていません。 ユーザーは、複雑さまたは強度の要件を満たすパスワードを定義する必要があります。 |
| パスワードがあいまいポリシーに準拠していません | 使用されたパスワードは[禁止パスワード リスト](./concept-password-ban-bad.md#how-are-passwords-evaluated)に表示され、使用することはできません。 ユーザーは、禁止パスワード リストのポリシーを満たしているかそれ以上の強度のパスワードを定義する必要があります。 |

## <a name="sspr-errors-that-a-user-might-see"></a>ユーザーに表示される可能性のある SSPR エラー

次のエラーと技術的な詳細は、SSPR 処理の一環としてユーザーに表示される場合があります。 多くの場合、SSPR 機能を有効にしたり、構成したり、アカウントを登録したりする必要があるため、ユーザーが自分で解決できるエラーではありません。

次の情報を使用して、問題と、Azure AD テナントまたは個々のユーザー アカウントで修正する必要があることを理解してください。

| エラー | 詳細 | 技術的な詳細 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 申し訳ありません。管理者が組織でのパスワードのリセットを無効にしているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、この機能を有効にするように依頼してください。<br /><br />詳細については、[Azure AD パスワードを忘れた場合](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions)に関する記事をご覧ください。 | SSPR_0009: パスワードのリセットが管理者によって有効にされていないことが検出されました。 管理者に連絡して、組織のパスワードのリセットを有効にするように依頼してください。 |
| WritebackNotEnabled = 10 |申し訳ありません。管理者が組織に必要なサービスを有効にしていないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、組織の構成を確認するように依頼してください。<br /><br />この必要なサービスの詳細については、[パスワード ライトバックの構成](./tutorial-enable-sspr-writeback.md)に関する記事をご覧ください。 | SSPR_0010: パスワード ライトバックが有効になっていないことが検出されました。 管理者に連絡して、パスワード ライトバックを有効にするように依頼してください。 |
| SsprNotEnabledInUserPolicy = 11 | 申し訳ありません。管理者が組織のパスワード リセットを構成していないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、パスワードのリセットを構成するように依頼してください。<br /><br />パスワードのリセット構成の詳細については、[Azure AD のセルフサービス パスワード リセット のクイック スタート](./tutorial-enable-sspr.md)に関する記事をご覧ください。 | SSPR_0011: 組織でパスワード リセット ポリシーを定義していません。 管理者に連絡して、パスワード リセット ポリシーを定義するように依頼してください。 |
| UserNotLicensed = 12 | 申し訳ありません。組織で必要なライセンスが不足しているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、ご自分のライセンスの割り当てを確認するように依頼してください。<br /><br />ライセンスの詳細については、「[Azure AD のセルフサービス パスワード リセットのライセンス要件](./concept-sspr-licensing.md)」をご覧ください。 | SSPR_0012: 組織には、パスワードのリセットを実行するために必要なライセンスがありません。 管理者に連絡して、ライセンスの割り当てを見直すように依頼してください。 |
| UserNotMemberOfScopedAccessGroup = 13 | 申し訳ありません。管理者が、お使いのアカウントでパスワードのリセットを使用するように構成していないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、お使いのアカウントでパスワード リセットを構成するように依頼してください。<br /><br />パスワードのリセットのアカウント構成の詳細については、[ユーザーのパスワード リセットのロールアウト](./howto-sspr-deployment.md)に関する記事をご覧ください。 | SSPR_0013:パスワード リセットが有効になっているグループのメンバーではありません。 管理者に連絡して、このグループに追加してもらうよう依頼してください。 |
| UserNotProperlyConfigured = 14 | 申し訳ありません。お使いのアカウントでは必要な情報が不足しているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、パスワードをリセットしてもらうよう依頼してください。 ご自分のアカウントにもう一度アクセスできるようになったら、必要な情報を登録していただく必要があります。<br /><br />情報を登録するには、「[セルフサービス パスワード リセット を登録する](../user-help/active-directory-passwords-reset-register.md)」に記載されている手順に従ってください。 | SSPR_0014: パスワードをリセットするための追加のセキュリティ情報が必要です。 続行するには、管理者に連絡して、パスワードをリセットしてもらうよう依頼してください。 ご自分のアカウントにアクセスできるようになったら、 https://aka.ms/ssprsetup で追加のセキュリティ情報を登録することができます。 管理者は、[パスワードのリセットの認証データの設定と読み取り](howto-sspr-authenticationdata.md)に関する記事に記載された手順に従い、ご利用のアカウントに追加のセキュリティ情報を追加できます。 |
| OnPremisesAdminActionRequired = 29 | 申し訳ありません。組織のパスワード リセット構成に問題があるため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、調査するように依頼してください。 <br /><br />Or<br /><br />組織のパスワード リセット構成に問題があるため、現時点ではパスワードをリセットできません。 この問題を解決するためにご自分で行える処理はありません。 管理者に連絡して、調査するように依頼してください。<br /><br />潜在的な問題の詳細については、「[パスワード ライトバックのトラブルシューティング](troubleshoot-sspr-writeback.md)」をご覧ください。 | SSPR_0029: オンプレミス構成でのエラーのため、パスワードをリセットすることができません。 管理者に連絡して、調査するように依頼してください。 |
| OnPremisesConnectivityError = 30 | 申し訳ありません。組織への接続に問題があるため、現時点ではパスワードをリセットできません。 今すぐ実行すべきアクションはありませんが、あとでもう一度試してみて、問題が解決する場合があります。 問題が解決しない場合は、管理者に連絡して、調査するように依頼してください。<br /><br />接続の問題の詳細については、[パスワード ライトバックの接続のトラブルシューティング](troubleshoot-sspr-writeback.md)に関する記事をご覧ください。 | SSPR_0030: オンプレミス環境との接続状況が悪いため、パスワードをリセットできません。 管理者に連絡して、調査するように依頼してください。|

## <a name="azure-ad-forums"></a>Azure AD フォーラム

Azure AD やセルフサービス パスワード リセットに関する一般的な質問がある場合は、[Azure Active Directory に関する Microsoft Q&A 質問ページ](/answers/topics/azure-active-directory.html)でコミュニティに支援を求めることができます。 コミュニティのメンバーには、エンジニア、製品マネージャー、MVP、IT プロフェッショナルなどが含まれます。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

問題に対する回答が見つからない場合は、Microsoft のサポート チームが随時、問題の解決をお手伝いします。

適切なサポートを提供するため、ケースをオープンする際はできるだけ詳しい情報のご提供をお願いいたします。 これらの詳細には、次の内容が含まれています。

* **エラーの一般的な説明**: どのようなエラーですか。 どのような動作が見られましたか。 エラーを再現することはできますか。 できるだけ詳しくお知らせください。
* **ページ**: エラーが表示されたときに、どのページを表示していましたか。 可能な場合はそのページの URL とスクリーンショットをお送りください。
* **サポート コード**: エラーが表示されたときに生成されたサポート コードをお知らせください。
   * このコードを見つけるには、エラーを再現してから、画面の下部にある **サポート コード** のリンクを選択し、生成された GUID をサポート エンジニアに送信します。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="サポート コードは、Web ブラウザー ウィンドウの右下にあります。":::

  * ページの下部にサポート コードが表示されない場合は、F12 キーを押して SID と CID を検索し、この 2 つの結果をサポート エンジニアに送信します。
* **日付、時刻、タイム ゾーン**: エラーが発生した正確な日付、時刻、"*タイム ゾーン*" をお知らせください。
* **ユーザー ID**: エラーが表示されたユーザーをお知らせください。 たとえば、*user\@contoso.com* です。
   * このユーザーは、フェデレーション ユーザーですか。
   * このユーザーは、パススルー認証ユーザーですか。
   * このユーザーは、パスワード ハッシュ同期ユーザーですか。
   * このユーザーは、クラウド限定ユーザーですか。
* **ライセンス**: ユーザーに Azure AD のライセンスが割り当てられていますか。
* **アプリケーション イベント ログ**: パスワード ライトバックの使用中に、ご利用のオンプレミスのインフラストラクチャでエラーが発生した場合は、Azure AD Connect サーバーからのアプリケーション イベント ログのコピーを圧縮してお送りください。

## <a name="next-steps"></a>次の手順

SSPR の詳細については、「[Azure AD のセルフサービス パスワード リセットのしくみ](concept-sspr-howitworks.md)」または「[Azure Active Directory でのセルフサービス パスワード リセットによる書き戻しのしくみ](concept-sspr-writeback.md)」を参照してください。
