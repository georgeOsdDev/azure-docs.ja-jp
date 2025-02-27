---
title: Azure Policy でのスコープについて
description: Azure Resource Manager でのスコープの概念と、それがどのように Azure Policy に適用されて、Azure Policy がどのリソースを評価するかが制御されるしくみについて説明します。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90984435"
---
# <a name="understand-scope-in-azure-policy"></a>Azure Policy でのスコープについて

どのリソースが評価可能であるかと、どのリソースが Azure Policy によって評価されるかを決定する、多数の設定があります。 これらの制御の主要な概念が、"_スコープ_" です。
概要については、[Azure Resource Manager でのスコープ](../../../azure-resource-manager/management/overview.md#understand-scope)に関するセクションを参照してください。
この記事では、Azure Policy での "_スコープ_" と、それに関連するオブジェクトおよびプロパティについて説明します。

## <a name="definition-location"></a>定義の場所

Azure Policy によって使用される最初のインスタンスのスコープは、ポリシー定義が作成されるときです。 定義は、管理グループまたはサブスクリプションに保存できます。 この場所によって、イニシアティブまたはポリシーを割り当てることができるスコープが決まります。 リソースは、割り当ての対象になるには、定義の場所のリソース階層内にある必要があります。

定義の場所が:

- **サブスクリプション** の場合 - そのサブスクリプション内のリソースだけをポリシー定義に割り当てることができます。
- **管理グループ** の場合 - 子管理グループと子サブスクリプション内のリソースだけをポリシー定義に割り当てることができます。 ポリシー定義を複数のサブスクリプションに適用する予定がある場合、その場所は各サブスクリプションを含む管理グループである必要があります。

この場所は、ポリシー定義を使用するすべてのリソースによって共有されるリソース コンテナーである必要があります。 このリソース コンテナーは、通常、ルート管理グループの近くにある管理グループです。

## <a name="assignment-scopes"></a>割り当てスコープ

割り当てには、スコープを設定するいくつかのプロパティがあります。 これらのプロパティを使用して、どのリソースを Azure Policy で評価するかと、どのリソースをコンプライアンスで考慮するかを決定します。 これらのプロパティは、以下の概念と対応しています。

- 包含 - リソース階層または個々のリソースは、定義によってコンプライアンスを評価されます。 割り当てオブジェクトの `properties.scope` プロパティによって、何を包含し、コンプライアンスを評価するかが決まります。 詳細については、[割り当ての定義](./assignment-structure.md)に関するページを参照してください。

- 除外 - リソース階層または個々のリソースは、定義によってコンプライアンスを評価されません。 割り当てオブジェクトの `properties.notScopes` "_配列_" プロパティによって、何を除外するかが決まります。 これらのスコープ内のリソースは評価されず、コンプライアンスの対象になりません。 詳細については、[割り当ての定義の除外スコープ](./assignment-structure.md#excluded-scopes)に関するセクションを参照してください。

ポリシー割り当てのプロパティに加えて、[ポリシー適用除外](./exemption-structure.md)オブジェクトです。 適用除外により、評価されない割り当ての部分を識別する方法が提供されることで、スコープのストーリーが強化されます。

- 免除 (**プレビューで無料** の機能) - リソース階層または個々のリソースは、定義によってコンプライアンスを評価されるはずですが、別の方法によって適用除外や軽減が行われているために評価されません。 この状態のリソースは、追跡できるように、コンプライアンス レポートでは **免除** として表示されます。 免除オブジェクトは、リソース階層に対して、または子オブジェクトとして個々のリソースに対して作成されます。これにより、免除のスコープが決まります。 リソース階層または個々のリソースは、複数の割り当てから免除できます。 免除は、`expiresOn` プロパティを使用して、スケジュールに従って有効期限が切れるように構成できます。 詳細については、[免除の定義](./exemption-structure.md)に関するページを参照してください。

  > [!NOTE]
  > リソース階層または個々のリソースに免除を与えることによる影響のため、免除には追加のセキュリティ対策があります。 リソース階層または個々のリソースに対して `Microsoft.Authorization/policyExemptions/write` 操作を要求するだけでなく、免除の作成者は、ターゲットの割り当てに対する `exempt/Action` 動詞を持つ必要があります。

## <a name="scope-comparison"></a>スコープの比較

次の表は、スコープのオプションの比較です。

| | 包含 | 除外 (notScopes) | 除外 |
|---|:---:|:---:|:---:|
|**リソースが評価される** | &#10004; | - | - |
|**Resource Manager オブジェクト** | - | - | &#10004; |
|**ポリシー割り当てオブジェクトを変更する必要がある** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>次のステップ

- [ポリシー定義の構造](./definition-structure.md)についてさらに学習します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。