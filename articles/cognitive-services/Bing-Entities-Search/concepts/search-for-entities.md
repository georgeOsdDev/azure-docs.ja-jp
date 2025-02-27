---
title: Bing Entity Search API を使用してエンティティを検索する
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API を使用して、検索クエリからエンティティと場所を抽出して検索します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353291"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Bing Entity API でのエンティティの検索

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Bing Autosuggest API で検索語句の候補を表示する

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーが検索語句を入力した後、その語句を URL エンコードしたうえで、[q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) クエリ パラメーターを設定します。 たとえば、ユーザーが「*Marcus Appel*」と入力した場合、`q` を *Marcus+Appel* または *Marcus%20Appel* に設定します。

検索用語にスペル ミスが含まれていると、検索応答に [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) オブジェクトが含まれます。 このオブジェクトは元のスペルと Bing が検索に使用した修正済みのスペルを示します。

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Bing Entity Search API の応答

API の応答には、[SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse) オブジェクトが含まれます。 Bing で関連するエンティティまたは場所が検出された場合、オブジェクトには `entities` フィールド、`places` フィールド、またはその両方が含まれます。 それ以外の場合は、応答オブジェクトにはいずれのフィールドも含まれません。
> [!NOTE]
> エンティティの応答では複数の市場がサポートされますが、場所の応答では米国ビジネスの場所しかサポートされません。 

`entities` フィールドは、[Entity](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) オブジェクトのリストを含む [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)オブジェクトです (`value` フィールドを参照)。 リストには、1 つの主要なエンティティ、複数のあいまいさ排除エンティティ、またはその両方が含まれている場合があります。 

主要なエンティティは、要求を満たす唯一のエンティティだと Bing が認識したときに返されます (どのエンティティが要求を満たすかに関するあいまいさはありません)。 複数のエンティティが要求を満たす可能性がある場合は、リストには複数のあいまいさ排除エンティティが含まれます。 たとえば、要求で映画シリーズの一般的なタイトルを使用している場合、リストにはあいまいさ排除エンティティが含まれる可能性が高くなります。 しかし、要求でシリーズの特定のタイトルを指定した場合、リストには 1 つの主要なエンティティが含まれる可能性が高くなります。

エンティティには、有名な人物 (歌手、俳優、スポーツ選手、モデルなど)、場所やランドマーク (レーニア山やリンカーン記念館など)、もの (バナナ、ゴールデンドゥードル、本、映画のタイトルなど) が含まれます。 [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) フィールドには、エンティティの型を識別するためのヒントが含まれています。 たとえば、それが個人、映画、動物、またはアトラクションかどうかを識別します。 可能な型のリストについては、[エンティティ型](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)のセクションを参照してください。

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

主要なエンティティとあいまいさ排除エンティティを含む応答を次に示します。

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

このエンティティには、`name`、`description`、`image` フィールドが含まれています。 お使いのユーザー エクスペリエンスにこれらのフィールドを表示する場合は、これらに帰属を示す必要があります。 `contractualRules` フィールドには、適用する必要がある属性のリストが含まれています。 契約上の規則は、属性が適用されるフィールドを識別します。 属性の適用に関する詳細は、[属性](#data-attribution)のセクションを参照してください。

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

エンティティ情報 (名前、説明、イメージ) を表示する場合は、`webSearchUrl` フィールド内の URL を使用して、エンティティを含む Bing の検索結果ページにリンクする必要もあります。

## <a name="find-places"></a>場所を探す

`places` フィールドは、[Place](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) オブジェクトのリストを含む [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) オブジェクトです (詳しくは、[エンティティ型](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)に関する記事を参照)。 このリストには、要求を満たす 1 つまたは複数のローカル エンティティが含まれます。

場所には、レストラン、ホテル、または地元企業が含まれます。 [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) フィールドには、ローカル エンティティの型を識別するためのヒントが含まれています。 リストには、Place、LocalBusiness、Restaurant などのヒントのリストが含まれています。 配列内の連続する各ヒントにより、エンティティの型が絞り込まれます。 可能な型のリストについては、[エンティティ型](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)のセクションを参照してください。

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> エンティティの応答では複数の市場がサポートされますが、場所の応答では米国ビジネスの場所しかサポートされません。 

*近くのレストラン* などのローカル対応エンティティ クエリでは、正確な結果を提供するためにユーザーの場所を提供する必要があります。 要求では常に X-Search-Location と X-MSEdge-ClientIP のヘッダーを使用して、ユーザーの場所を指定する必要があります。 Bing では、ユーザーの場所をクエリに使用するメリットが認められると、[QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) の `askUserForLocation` フィールドが **true** に設定されます。 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

場所の結果には、場所の名前、住所、電話番号、エンティティの Web サイトの URL が含まれます。 エンティティ情報を表示する場合は、`webSearchUrl` フィールド内の URL を使用して、エンティティを含む Bing の検索結果ページにリンクする必要もあります。

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> お客様およびお客様の代理を務める第三者のいずれも、Entities API から取得したデータを Microsoft 以外のサービスまたは機能のテスト、開発、トレーニング、頒布、または提供を目的として利用、保持、保存、キャッシュ、共有、または配布することは認められません。  

## <a name="data-attribution"></a>データの帰属表示

Bing Entity API の応答には、第三者が所有する情報が含まれます。 お客様は、ユーザー エクスペリエンスで利用するクリエイティブ コモンズ ライセンスがあればそれを遵守するなど、情報を適切に利用する責任を負います。

回答または結果に `contractualRules`、`attributions`、`provider` のいずれかのフィールドが含まれている場合、データをそれらの帰属を示す必要があります。 回答にこれらのいずれのフィールドも含まれていない場合は、属性は必要ありません。 回答に `contractualRules` フィールドと、`attributions` フィールドまたは `provider` フィールド (またはその両方) が含まれている場合は、契約上の規則を使用してデータがそれらの帰属を示す必要があります。

次の例では、MediaAttribution タイプの契約規則を含むエンティティと、`provider` フィールドを含む画像を示しています。 この MediaAttribution 規則では、規則の対象として画像を指定しています。このため、帰属の表示にあたっては画像の `provider` フィールドが無視され、代わりに MediaAttribution 規則が使用されることになります。  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

契約規則に `targetPropertyName` フィールドが含まれている場合、規則は対象のフィールドにのみ適用されます。 含まれていない場合は、規則は `contractualRules` フィールドを含む親オブジェクトに適用されます。

次の例では、`LinkAttribution` 規則に `targetPropertyName` フィールドが含まれているため、`description` フィールドに規則が適用されます。 特定のフィールドに適用される規則の場合、対象となるデータの直後に提供元の Web サイトへのハイパーリンクを含めた 1 行を追加する必要があります。 たとえば、説明の帰属を示すには、プロバイダーの Web サイト上のデータへのハイパーリンクを含む説明テキストの直後に 1 行含めます。このケースでは、contoso.com へのリンクを作成します。

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>ライセンスの帰属表示

契約規則のリストに [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution) 規則が含まれている場合、ライセンスが適用されるコンテンツの直後の行に通知を表示する必要があります。 `LicenseAttribution` 規則では、ライセンスが適用されるプロパティを特定するために `targetPropertyName` フィールドを使用します。

`LicenseAttribution` 規則を含む例を次に示します。

![ライセンスの帰属表示](../media/cognitive-services-bing-entities-api/licenseattribution.png)

表示するライセンス通知には、ライセンスに関する情報を掲載している Web サイトへのハイパーリンクを含める必要があります。 通常は、ライセンスの名前をハイパーリンクにします。 たとえば、通知の文言が "**Text under CC-BY-SA license (CC-BY-SA ライセンスが適用されるテキスト)**" であって、ライセンスの名前が CC-BY-SA の場合、CC-BY-SA の部分をハイパーリンクにします。

### <a name="link-and-text-attribution"></a>リンクとテキスト属性

[LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) と [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) の規則は通常、データの提供元を明らかにする場合に使用します。 `targetPropertyName` フィールドは、規則が適用されるフィールドを特定するためのものです。

プロバイダーの帰属を示すには属性を適用するコンテンツ (対象となるフィールドなど) の直後に 1 行含めます。 追加した行には、提供元がデータの出典であることを明確に示したラベルを付ける必要があります。 たとえば、"データ元: contoso.com" のようにします。 `LinkAttribution` 規則の場合、提供元の Web サイトへのハイパーリンクを作成する必要があります。

`LinkAttribution` および `TextAttribution` の規則を含む例を次に示します。

![リンクとテキストの帰属表示](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>メディアの帰属表示

エンティティに画像が含まれており、その画像を表示する場合には、提供元の Web サイトへのクリックスルー リンクを提供する必要があります。 エンティティに [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution) 規則が含まれている場合、その規則の URL を使用してクリックスルー リンクを作成します。 含まれていない場合は、画像の `provider` フィールドに含まれる URL を使用してクリックスルー リンクを作成します。

画像の `provider` フィールドと契約規則が含まれる例を次に示します。 例には契約上の規則が含まれているため、イメージの `provider` フィールドを無視して、`MediaAttribution` 規則を適用します。

![メディアの帰属表示](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>検索または検索と同様のエクスペリエンス

Bing Web Search API と同じく、Entity Search API は、直接ユーザー クエリまたは検索の結果として、またはアプリまたはエクスペリエンス内のユーザーの検索結果として論理的に解釈できるアクションの結果としてのみ使用できます。 わかりやすくするため、許容できる検索または検索に似たエクスペリエンスの例をいくつか次に示します。

- ユーザーがアプリ内の検索ボックスに直接クエリを入力する
- ユーザーが特定のテキストまたはイメージを選択し、"詳細" または "追加情報" を要求する
- ユーザーが特定のトピックに関する検索ボットを要求する
- ユーザーが Visual Search の種類のシナリオで、特定のオブジェクトまたはエンティティについて詳しく説明する

エクスペリエンスが検索と同様のエクスペリエンスと見なせるかどうかわからない場合は、Microsoft に確認することをお勧めします。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次のステップ

* Bing Entity Search API でのエンティティの検索を始めるには、[クイック スタート](../quickstarts/csharp.md)を試してください。