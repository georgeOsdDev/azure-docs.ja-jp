---
title: Azure Front Door ルール エンジンのアクション
description: この記事では、Azure Front Door ルール エンジンでできるさまざまなアクションの一覧を提供します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569787"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azure Front Door ルール エンジンのアクション

[AFD ルール エンジン](front-door-rules-engine.md)では、ルールは 0 個以上の一致条件とアクションで構成されます。 この記事では、AFD ルール エンジンで使用できるアクションについて詳しく説明します。

アクションは、1 つの一致条件または一致条件のセットによって識別される要求の種類に適用される動作を定義します。 AFD ルール エンジンでは、1 つのルールに最大 5 つのアクションを含めることができます。 そのうちの 1 つのみをルート構成オーバーライド アクション (転送またはリダイレクト) にすることができます。

Azure Front Door ルール エンジンでは、次のアクションを使用できます。  

## <a name="modify-request-header"></a>要求ヘッダーの変更

配信元に送信される要求に含まれるヘッダーを変更するには、このアクションを使用します。

### <a name="required-fields"></a>必須フィールド

アクション | HTTP ヘッダー名 | 値
-------|------------------|------
Append | このオプションが選択され、ルールが一致する場合、**ヘッダー名** に指定されたヘッダーが、指定された値で要求に追加されます。 ヘッダーが既に存在する場合、値は既存の値に追加されます。 | String
Overwrite | このオプションが選択され、ルールが一致する場合、**ヘッダー名** に指定されたヘッダーが、指定された値で要求に追加されます。 ヘッダーが既に存在する場合、指定された値によって既存の値が上書きされます。 | String
削除 | 一致するルールによってこのオプションが選択され、ルールで指定されたヘッダーが存在する場合、ヘッダーは要求から削除されます。 | String

## <a name="modify-response-header"></a>応答ヘッダーの変更

クライアントに返される応答に含まれるヘッダーを変更するには、このアクションを使用します。

### <a name="required-fields"></a>必須フィールド

アクション | HTTP ヘッダー名 | 値
-------|------------------|------
Append | このオプションが選択され、ルールが一致する場合、**ヘッダー名** に指定されたヘッダーが、指定された **値** を使用して応答に追加されます。 ヘッダーが既に存在する場合、**値** は既存の値に追加されます。 | String
Overwrite | このオプションが選択され、ルールが一致する場合、**ヘッダー名** に指定されたヘッダーが、指定された **値** を使用して応答に追加されます。 ヘッダーが既に存在する場合、**値** によって既存の値が上書きされます。 | String
削除 | このオプションが選択され、ルールが一致し、ルールで指定されたヘッダーが存在する場合、ヘッダーは応答から削除されます。 | String

## <a name="route-configuration-overrides"></a>ルート構成のオーバーライド 

### <a name="route-type-redirect"></a>ルートの種類: リダイレクト

クライアントを新しい URL にリダイレクトするには、このアクションを使用します。 

#### <a name="required-fields"></a>必須フィールド

フィールド | 説明 
------|------------
リダイレクトの種類 | 要求元に返される応答タイプを選択します:検出 (302)、移動 (301)、一時リダイレクト (307)、および永続的なリダイレクト (308)。
プロトコルをリダイレクトします | 一致要求、HTTP、HTTPS。
宛先ホスト | 要求のリダイレクト先のホスト名を選択します。 受信ホストを保持するには、空のままにします。
宛先のパス | リダイレクトで使用するパスを定義します。 受信パスを保持するには、空のままにします。  
クエリ文字列 | リダイレクトで使用されるクエリ文字列を定義します。 受信クエリ文字列を保持するには、空のままにします。 
宛先フラグメント | リダイレクトで使用するフラグメントを定義します。 受信フラグメントを保持するには、空のままにします。 


### <a name="route-type-forward"></a>ルートの種類: 転送

クライアントを新しい URL に転送するには、このアクションを使用します。 このアクションには、URL の書き換えとキャッシュのためのサブアクションも含まれています。 

フィールド | 説明 
------|------------
バックエンド プール | オーバーライドして要求を処理するバックエンド プールを選択します。これにより、現在 Front Door プロファイルにあるすべての構成済みバックエンド プールも表示されます。 
転送プロトコル | 一致要求、HTTP、HTTPS。
URL 書き換え | このアクションを使用して、送信元への途上で要求のパスを書き直します。 有効にする場合は、次の追加の必須フィールドを確認してください
キャッシュ | Enabled、Disabled 有効にする場合は、次の追加の必須フィールドを確認してください。 

#### <a name="url-rewrite"></a>URL 書き換え

この設定を使用して、バックエンドに転送する要求を作成するときに使用するオプションの **カスタム転送パス** を構成します。

フィールド | 説明 
------|------------
カスタム転送パス | 要求の転送先のパスを定義します。 

#### <a name="caching"></a>キャッシュ

これらの設定を使用して、クエリ文字列を含む要求に対してファイルをキャッシュする方法を制御します。 すべてのパラメーターまたは選択したパラメーターに基づいてコンテンツをキャッシュするかどうかも制御します。 追加の設定を使用して有効期限 (TTL) 値を上書きすると、コンテンツがキャッシュに保持される時間を制御できます。 アクションとしてキャッシュを強制するには、[キャッシュ] フィールドを [有効] に設定します。 キャッシュを強制すると、次のオプションが表示されます。 

キャッシュの動作 |  説明              
---------------|----------------
クエリ文字列を無視 | 資産がキャッシュされると、キャッシュされた資産の有効期限が切れるまで、続くすべての要求でクエリ文字列が無視されます。
一意の URL をすべてキャッシュ | クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。
Ignore specified query strings (指定されたクエリ文字列を無視する) | [クエリ パラメーター] 設定に一覧表示されている要求 URL クエリ文字列は、キャッシュ対象として無視されます。
Include specified query strings (指定されたクエリ文字列を含める) | [クエリ パラメーター] 設定に一覧表示されている要求 URL クエリ文字列は、キャッシュ対象として使用されます。

追加フィールド |  説明 
------------------|---------------
動的な圧縮 | Front Door はエッジのコンテンツを動的に圧縮できるため、応答が小さく高速になります。
クエリ パラメーター | キャッシュの基礎として使用する、許可 (または禁止) パラメーターのコンマ区切りのリスト。
Cache duration (キャッシュ期間) | キャッシュの有効期限 (日数、時間、分、秒)。 すべての値を Int にする必要があります。 

## <a name="next-steps"></a>次のステップ

- 最初の[ルール エンジン](front-door-tutorial-rules-engine.md)を構成する方法について学習します。 
- [ルール エンジンの一致条件](front-door-rules-engine-match-conditions.md)の詳細を確認します
- [Azure Front Door のルール エンジン](front-door-rules-engine.md)の詳細を確認します
