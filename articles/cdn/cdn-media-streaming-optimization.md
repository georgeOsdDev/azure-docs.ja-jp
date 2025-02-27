---
title: Azure CDN によるメディア ストリーミングの最適化
description: 部分的キャッシュ共有やキャッシュ フィル待機時間など、Azure Content Delivery Network のメディア ストリーミングを最適化するためのオプションについて説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192608"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Azure CDN によるメディア ストリーミングの最適化 
 
インターネット上での高解像度ビデオの使用はますます増加していますが、そのために大きなファイルの効率的な配信が難しくなっています。 お客様は、全世界のさまざまなネットワークやクライアントがビデオ オン デマンドやライブ ビデオ アセットをスムーズに再生できることを期待しています。 メディア ストリーミング ファイルの高速で効率的な配信メカニズムは、お客様がスムーズで楽しい経験をすることを可能にするために極めて重要です。  

サイズが大きく同時に視聴するユーザー数が多いため、ライブ ストリーミング メディアは特に配信が困難です。 遅延が長くなると、ユーザーが利用をやめる一因にもなります。 ライブ ストリームは事前にキャッシュ化できず、大きな遅延が視聴者に受け入れられることはないため、ビデオ フラグメントをタイムリーに配信する必要があります。 

ストリーミングの要求パターンも新しい困難をもたらします。 人気のあるライブ ストリームや、ビデオ オン デマンドの新シリーズがリリースされると、数千人から数百万人の視聴者が同時にストリームを要求する場合があります。 この場合、アセットがまだキャッシュされていないときに配信元サーバーがパンクしないように要求を賢く整理することが重要です。
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Azure CDN from Microsoft のメディア ストリーミングの最適化

**Azure CDN Standard from Microsoft** エンドポイントでは、最適化の種類として一般的な Web 配信を使用して、ストリーミング メディア資産を直接配信します。 

**Azure CDN Standard from Microsoft** のメディア ストリーミングの最適化は、配信に個々のメディア フラグメントを使用するライブまたはビデオ オン デマンド ストリーミング メディアに効果的です。 これは、1 つの大きな資産をプログレッシブ ダウンロードやバイト範囲要求で転送する場合の処理と異なるプロセスになります。 メディア配信のスタイルについては、「[Azure CDN を介した大きなファイルのダウンロードの最適化](cdn-large-file-optimization.md)」を参照してください。

一般的なメディア配信またはビデオ オン デマンドによるメディア配信の最適化のタイプでは、バックエンドの最適化を用いた Azure Content Delivery Network (CDN) を使用することによって、メディア資産のより迅速な配信が可能になります。 さらに、時間をかけて学習したベスト プラクティスに基づいてメディア資産の構成を使用します。

### <a name="partial-cache-sharing"></a>Partial Cache Sharing (部分キャッシュ共有)
部分キャッシュ共有を使用すると、CDN は、新たな要求に対して部分的にキャッシュされたコンテンツを提供できます。 たとえば、CDN への最初の要求がキャッシュ ミスになり、その要求は配信元に送信されます。 この不完全なコンテンツが CDN のキャッシュに読み込まれた場合でも、CDN へのその他の要求がこのデータの取得を開始します。 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Azure CDN from Verizon のメディア ストリーミングの最適化

**Azure CDN Standard from Verizon** エンドポイントおよび **Azure CDN Premium from Verizon** エンドポイントでは、最適化の種類として一般的な Web 配信を使用して、ストリーミング メディア資産を直接配信します。 CDN には、既定でメディア アセットの配信を直接支援す機能もいくつかあります。

### <a name="partial-cache-sharing"></a>Partial Cache Sharing (部分キャッシュ共有)

部分キャッシュ共有を使用すると、CDN は、新たな要求に対して部分的にキャッシュされたコンテンツを提供できます。 たとえば、CDN への最初の要求がキャッシュ ミスになり、その要求は配信元に送信されます。 この不完全なコンテンツが CDN のキャッシュに読み込まれた場合でも、CDN へのその他の要求がこのデータの取得を開始します。 

### <a name="cache-fill-wait-time"></a>キャッシュ フィル待機時間

 キャッシュ フィル待機時間機能は、配信元サーバーから HTTP 応答ヘッダーが到着するまで、同じリソースの後続のすべての要求をエッジ サーバーに強制的に保留させます。 タイマーの有効期限が切れる前に配信元から HTTP 応答ヘッダーが到着すると、保留になったすべての要求はキャッシュを増大することなく使用されます。 同時に、配信元からのデータによってキャッシュがフィルされます。 既定では、キャッシュ フィル待機時間は 3000 ミリ秒に設定されます。 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Azure CDN from Akamai のメディア ストリーミングの最適化
 
**Azure CDN Standard from Akamai** では、ストリーミング メディア資産を世界中のユーザーに大規模かつ効率的に配信する機能を提供します。 この機能では、配信元サーバーの負荷を軽減することで、待ち時間が短縮されます。 この機能は、Standard Akamai 価格レベルで利用できます。 

**Azure CDN Standard from Akamai** のメディア ストリーミングの最適化は、配信に個々のメディア フラグメントを使用するライブまたはビデオ オン デマンド ストリーミング メディアに効果的です。 これは、1 つの大きな資産をプログレッシブ ダウンロードやバイト範囲要求で転送する場合の処理と異なるプロセスになります。 メディア配信のスタイルについて詳しくは、「[大きなファイルの最適化](cdn-large-file-optimization.md)」をご覧ください。

一般的なメディア配信またはビデオ オン デマンドによるメディア配信の最適化のタイプでは、バックエンドの最適化を用いた CDN を使用することによって、メディア資産のより迅速な配信が可能になります。 さらに、時間をかけて学習したベスト プラクティスに基づいてメディア資産の構成を使用します。

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>メディア ストリーミングを最適化するように Akamai CDN エンドポイントを構成する
 
コンテンツ配信ネットワーク (CDN) エンドポイントは、Azure Portal 経由での大容量ファイルの配信を最適化するように構成することができます。 REST API またはクライアント SDK を使用してこれを行うこともできます。 次の手順は、Azure Portal を使用した、**Azure CDN Standard from Akamai** プロファイルのプロセスを示しています。

1. 新しいエンドポイントを追加するには、Akamai の **[CDN プロファイル]** ページで **[エンドポイント]** を選択します。
  
    ![新しいエンドポイント](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. **[最適化対象]** ドロップダウン リストで、ビデオ オン デマンド資産の **[ビデオ オン デマンド メディア ストリーミング]** を選択します。 ライブとビデオ オン デマンド ストリーミングを組み合わせる場合は、 **[一般的なメディア ストリーミング]** を選択します。

    ![選択したストリーミング](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
エンドポイントを作成すると、特定の条件に一致するすべてのファイルに対して最適化が適用されます。 次のセクションでは、この処理について詳しく説明します。 

### <a name="caching"></a>キャッシュ

**Azure CDN Standard from Akamai** では、資産がストリーミング マニフェストまたはフラグメントであることを検出すると、一般的な Web 配信とは異なるキャッシュの有効期限を使用します。 (以下の表の詳細な一覧を参照)。通常の処理と同様に、配信元から送信される cache-control または Expires ヘッダーが優先されます。 資産がメディア資産でない場合は、一般的な Web 配信の有効期限を使用してキャッシュされます。

多くのユーザーが、まだ存在していないフラグメントを要求する場合の配信元のオフロードには、負の短いキャッシュ時間が便利です。 例として、瞬間的に配信元からパケットが利用できなくなることのあるライブ ストリームが挙げられます。 通常、ビデオ コンテンツは変更されないため、キャッシュ間隔を長くしても配信元からの要求をオフロードできます。

| キャッシュ  | 一般的な Web 配信 | 一般的なメディア ストリーミング | ビデオ オン デマンド メディア ストリーミング  
|--- | --- | --- | ---
| キャッシュ:Positive <br> HTTP 200、203、300、 <br> 301、302、410 | 7 日 |365 日 | 365 日   
| キャッシュ:Negative <br> HTTP 204、305、404、 <br> 405 | なし | 1 秒 | 1 秒
 
### <a name="deal-with-origin-failure"></a>配信元のエラーの処理  

一般的なメディア配信とビデオ オン デマンド メディア配信には、一般的な要求パターンのベスト プラクティスに基づく配信元のタイムアウトと再試行ログもあります。 たとえば、一般的なメディア配信は、ライブおよびビデオ オン デマンド メディア配信を対象としているため、ライブ ストリーミングの時間に厳しい特性により、短い接続タイムアウトが使用されます。

接続がタイムアウトになると、CDN は一定回数再試行した後、クライアントに「504 ゲートウェイ タイムアウト」エラーを送信します。 

ファイルがファイルの種類とサイズ条件の一覧と一致すると、CDN はメディア ストリーミングの動作を使用します。 それ以外の場合には一般的な Web 配信を使用します。
   
### <a name="conditions-for-media-streaming-optimization"></a>メディア ストリーミングの最適化の条件 

次の表に、メディア ストリーミングの最適化で満たす必要のある一連の条件を示します。 
 
サポート対象のストリーミング タイプ | ファイル拡張子  
--- | ---  
Apple HLS | m3u8、m3u、m3ub、key、ts、aac
Adobe HDS | f4m、f4x、drmmeta、bootstrap、f4f、<br>Seg-Frag URL 構造 <br> (対応する正規表現: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd、dash、divx、ismv、m4s、m4v、mp4、mp4v、 <br> sidx、webm、mp4a、m4a、isma
スムーズ ストリーミング | /manifest/, /QualityLevels/Fragments/
  
 
