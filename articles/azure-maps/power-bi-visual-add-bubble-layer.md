---
title: Azure Maps Power BI ビジュアルにバブル レイヤーを追加する | Microsoft Azure Maps
description: この記事では、Power BI 用の Microsoft Azure Maps ビジュアルでバブル レイヤーを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896227"
---
# <a name="add-a-bubble-layer"></a>バブル レイヤーを追加する

**バブル レイヤー** は、場所データをマップ上で拡大縮小された円としてレンダリングします。

> [!div class="mx-imgBorder"]
> ![バブル レイヤーを使用してポイント データを表示しているマップ](media/power-bi-visual/bubble-layer-with-legend-color.png)

最初はすべてのバブルの塗りつぶしの色は同じです。 フィールドが、 **[フィールド]** ペインの **[凡例]** バケットに渡されると、その分類に基づいてバブルが色分けされます。 バブルの枠線は既定では白ですが、新しい色に変更したり、ハイコントラストの枠線オプションを有効にして変更したりすることもできます。 **[High-contrast outline]\(ハイコントラストの枠線\)** オプションは、塗りつぶしの色のハイコントラストのバリエーションである枠線の色を動的に割り当てます。 これにより、マップのスタイルに関係なく、バブルが確実にはっきり表示されるようになります。 **[バブル レイヤー]** セクションで使用できる **[Format]\(形式\)** ペインの主な設定を次に示します。

| 設定               | 説明    |
|-----------------------|----------------|
| サイズ                  | 各バブルのサイズ。 このオプションは、フィールドが **[フィールド]** ペインの **[サイズ]** バケットに渡されると非表示になります。 この記事で後述する「[バブルのサイズの拡大縮小](#bubble-size-scaling)」トピックで説明されているように、追加のオプションが表示されます。 |
| 塗りつぶしの色            | 各バブルの色。 このオプションは、フィールドが **[フィールド]** ペインの **[凡例]** バケットに渡されると非表示になり、別の **[データの色]** セクションが **[Format]\(形式\)** ペインに表示されます。 |
| 塗りつぶしの透過性     | 各バブルの透明度。 |
| High-contrast outline\(ハイコントラストの枠線\) | アクセシビリティを向上させるために、塗りつぶしの色のハイコントラストのバリエーションを使用して、輪郭の色と塗りつぶしの色のコントラストを作成します。 |
| 輪郭の色         | バブルの輪郭を示す色。 このオプションは、 **[High-contrast outline]\(ハイコントラストの枠線\)** オプションが有効になっている場合は非表示になります。 |
| Outline transparency\(輪郭の透過性\)  | 輪郭の透過性。 |
| 輪郭の幅         | 輪郭の幅 (ピクセル)。 |
| ぼかし                  | 輪郭に適用されるぼかしの量。 値 1 は、中心点だけが透明にならないようにバブルをぼかします。 値 0 は、ぼかし効果を適用します。 |
| Pitch alignment\(ピッチの配置\)       | マップをピッチ調整するときのバブルの外観を指定します <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• ビューポート - バブルは、ビューポートを基準にしてマップ上の端に表示されます。 (既定値)。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• マップ - バブルは、マップの表面にフラットにレンダリングされます。 |
| ズーム スケール            | バブルの量は、ズーム レベルを基準に拡大縮小する必要があります。 ズーム スケールが 1 の場合、拡大縮小は行われません。 大きい値を指定すると、バブルは縮小時に小さくなり、拡大時に大きくなります。 これにより、縮小時にマップの乱雑さを軽減し、拡大時にポイントを目立たせることができます。 値 1 は、拡大縮小を適用しません。 |
| Min zoom\(最小ズーム\)              | タイルで使用可能な最小ズーム レベル。 |
| Max zoom\(最大ズーム\)              | タイルで使用可能な最大ズーム レベル。 |
| Layer position\(レイヤーの位置\)        | 他のマップ レイヤーに対するレイヤーの位置を指定します。 |

## <a name="bubble-size-scaling"></a>バブルのサイズの拡大縮小

フィールドが **[フィールド]** ペインの **[サイズ]** バケットに渡されると、バブルは、各データ ポイントのメジャー値に対して相対的に拡大縮小されます。 バブルの半径は最小値と最大値の間で拡大縮小されるので、 **[フォーマット]** ペインの **[バブル レイヤー]** セクションの **[サイズ]** オプションは、フィールドが **[サイズ]** バケットに渡されると非表示になります。 次のオプションは、 **[サイズ]** バケットにフィールドが指定されている場合に、 **[Format]\(形式\)** ペインの **[バブル レイヤー]** セクションに表示されます。

| 設定             | 説明  |
|---------------------|--------------|
| 最小サイズ            | データをスケーリングする場合のバブルの最小サイズ。|
| 最大サイズ            | データをスケーリングする場合のバブルの最大サイズ。|
| サイズの拡大縮小方法 | 相対的なバブル サイズを決定するために使用される拡大縮小アルゴリズム。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• 線形 - 最小サイズと最大サイズに線形にマップされる入力データの範囲。 (既定値)。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 対数 - 最小サイズと最大サイズに対数的にマップされる入力データの範囲。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 3 次ベジエ - 3 次ベジエ曲線の X1、Y1、X2、Y2 値を指定して、カスタムの拡大縮小方法を作成します。 |

**[サイズの拡大縮小方法]** が **[対数]** に設定されている場合、次のオプションが使用可能になります。

| 設定   | 説明      |
|-----------|------------------|
| 対数目盛 | バブルのサイズを計算するときに適用する対数目盛。 |

**[サイズの拡大縮小方法]** が **[3 次ベジエ]** に設定されている場合、拡大縮小曲線をカスタマイズするための次のオプションが使用可能になります。

| 設定 | 説明                           |
|---------|---------------------------------------|
| X1      | 3 次ベジエ曲線の X1 パラメーター。 |
| Y1      | 3 次ベジエ曲線の X2 パラメーター。 |
| X2      | 3 次ベジエ曲線の Y1 パラメーター。 |
| Y2      | 3 次ベジエ曲線の Y2 パラメーター。 |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) には、3 次ベジエ曲線のパラメーターを作成するための便利なツールが用意されています。

## <a name="next-steps"></a>次のステップ

マップにデータを表示する方法を変更する:

> [!div class="nextstepaction"]
> [横棒グラフ レイヤーを追加する](power-bi-visual-add-bar-chart-layer.md)

マップにコンテキストをさらに追加する:

> [!div class="nextstepaction"]
> [参照レイヤーを追加する](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [タイル レイヤーを追加する](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [リアルタイム トラフィックを表示する](power-bi-visual-show-real-time-traffic.md)

ビジュアルをカスタマイズする:

> [!div class="nextstepaction"]
> [Power BI における色の書式設定に関するヒントとコツ](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [視覚エフェクトのタイトル、背景、および凡例をカスタマイズする](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)