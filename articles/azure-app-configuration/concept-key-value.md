---
title: Azure App Configuration のキーと値のストアについて
description: 構成データをキー値として格納する Azure App Configuration のキー値ストレージについて説明します。 キー値は、アプリケーション設定の 1 つの表現です。
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930247"
---
# <a name="keys-and-values"></a>キーと値

Azure App Configuration では、構成データがキー値として格納されます。 キー値は、開発者によって使用されるアプリケーション設定のシンプルで柔軟な表現です。

## <a name="keys"></a>[キー]

キーは、キー値の識別子としての役割を果たし、対応する値を格納したり取得したりする際に使用されます。 区切り記号 (`/` や `:` など) を使用して、階層型名前空間にキーを整理するのが一般的です。 ご自分のアプリケーションに最も適した規則を使用してください。 App Configuration は、キーを 1 つのまとまりとして扱います。 キーを解析してその名前がどのような構造になっているのかを推測したり、名前に対して規則を適用したりすることはありません。

コンポーネント サービスに基づいて階層的に構造化されたキー名の例を次に示します。

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

キー値に対する具体的な名前付けスキームは、アプリケーション フレームワークにおける構成データの使われ方で決まる場合があります。 たとえば、Java の Spring Cloud フレームワークでは、Spring アプリケーションの設定を指定する `Environment` リソースを定義します。  これらは、"*アプリケーション名*" と "*プロファイル*" が含まれている変数によってパラメーター化されます。 通常、Spring Cloud に関連した構成データのキーは、区切り記号でつながれた、この 2 つの要素で始まります。

App Configuration に格納されるキーは Unicode ベースの文字列で、大文字と小文字が区別されます。 *app1* と *App1* というキーは App Configuration ストアでは区別されます。 アプリケーション内で構成設定を使用する際は、この点に留意してください。一部のフレームワークでは、構成キーの大文字と小文字が区別されないためです。 異なるキーを大文字と小文字で区別することは推奨されません。

キー名には任意の Unicode 文字を使用できますが、`%` は例外です。 キー名を `.` または `..` にすることはできません。 キー値にはサイズの上限があり、全部合わせて 10 KB までとなります。 この上限には、キーに使用されているすべての文字とその値、そして関連するすべてのオプション属性が含まれます。 キーには、この制限の範囲内で、いくつでも階層レベルを設けることができます。

### <a name="design-key-namespaces"></a>キーの名前空間の設計

一般に、構成データに使用するキーには、フラット型と階層型の 2 とおりの命名方法があります。 アプリケーションの使用上の観点から言えばどちらの方法も似ていますが、階層型の命名方法には、次のような各種利点があります。

* 読みやすい。 階層型のキー名では、区切り記号が文中のスペースの働きをします。 また、単語間の区切りも自然です。
* 管理しやすい。 キー名の階層は、構成データの論理上のグループを表現します。
* 使いやすい。 階層構造からキーをパターンマッチングして構成データの一部だけを取得するようなクエリが書きやすくなります。 さらに、多くの比較的新しいプログラミング フレームワークは、アプリケーションから特定の構成設定を利用できるよう、階層型の構成データをネイティブでサポートしています。

App Configuration では、さまざまな方法でキーを階層に分けて整理することができます。 そうしたキーは [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) と考えてください。 それぞれの階層キーはリソースの "*パス*" です。これは 1 つのコンポーネントか、または区切り記号で結合された複数のコンポーネントで構成されます。 区切り記号として使用する文字は、実際のアプリケーション、プログラミング言語、またはフレームワークの要件に基づいて選択します。 App Configuration では、キーごとに異なる区切り記号を使用することができます。

### <a name="label-keys"></a>ラベルのキー

App Configuration のキー/値には、必要に応じてラベル属性を割り当てることができます。 ラベルは、同じキーを持つ複数のキー/値を区別する目的で使用されます。 ラベル *A* とラベル *B* を持つキー *app1* は、App Configuration ストア内で 2 つの独立したキーになります。 既定では、キー値にはラベルがありません。 ラベルがないキー値を明示的に参照する場合は、`\0` (URL エンコード: `%00`) を使用してください。

ラベルは、キーのバリアントを作成するための便利な手段です。 ラベルは一般に、同じキーに対して複数の環境を指定する場合に使用します。

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>バージョンのキー値

ラベルは、同じキー値の複数のバージョンを作成する手段として使用します。 たとえば、アプリケーションのバージョン番号や Git のコミット ID をラベルとして入力すれば、特定のソフトウェア ビルドに関連付けられているキー/値を識別することが可能です。

> [!NOTE]
> 変更バージョンを探している場合、App Configuration では、過去の特定の期間に発生したすべてのキー値の変更が自動的に保持されています。 詳細については、「[ポイントインタイム スナップショット](./concept-point-time-snapshot.md)」を参照してください。

### <a name="query-key-values"></a>キー値のクエリ

それぞれのキー/値は、対応するキーとラベル (`\0` の場合もあり) によって一意に識別されます。 キー値は、パターンを指定して App Configuration ストアにクエリします。 App Configuration ストアからは、対応する値と属性を含む、そのパターンに一致するすべてのキー値が返されます。 App Configuration に対する REST API 呼び出しには、次のキー パターンを使用します。

| Key | 説明 |
|---|---|
| `key` を省略 (または `key=*`) | すべてのキーに一致します |
| `key=abc` | 厳密に **abc** であるキー名に一致します |
| `key=abc*` | **abc** で始まるキー名に一致します |
| `key=abc,xyz` | キー名 **abc** または **xyz** に一致します。 (CSV の上限は 5 つ) |

また、次のラベル パターンを含めることもできます。

| Label | 説明 |
|---|---|
| `label` を省略 (または `label=*`) | 任意のラベル (`\0` を含む) に一致します |
| `label=%00` | `\0` ラベルに一致します |
| `label=1.0.0` | 厳密に **1.0.0** であるラベルに一致します |
| `label=1.0.*` | **1.0.** で始まるラベルに一致します |
| `label=%00,1.0.0` | ラベル `\0` または **1.0.0** に一致します (CSV の上限は 5 つ) |

> [!NOTE]
> `*`、 `,`、および `\` はクエリの予約文字です。 キー名またはラベルに予約文字が使用されている場合は、クエリで `\{Reserved Character}` を使用することでこれをエスケープする必要があります。

## <a name="values"></a>値

キーに割り当てられる値も Unicode 文字列です。 値には、すべての Unicode 文字を使用できます。

### <a name="use-content-type"></a>Content-Type を使用する
App Configuration の各キー値には、content-type 属性があります。 必要に応じて、この属性を使用してキー値の値の型に関する情報を格納し、アプリケーションでそれを適切に処理するために役立てることができます。 content-type には任意の形式を使用できます。 App Configuration では、機能フラグ、Key Vault 参照、JSON キー値などの組み込みのデータ型に [メディアの種類]( https://www.iana.org/assignments/media-types/media-types.xhtml) (MIME の種類とも呼ばれます) が使用されます。

## <a name="next-steps"></a>次のステップ

* [ポイントインタイム スナップショット](./concept-point-time-snapshot.md)
* [機能の管理](./concept-feature-management.md)
* [イベント処理](./concept-app-configuration-event.md)
