---
title: Standard Encoder の形式およびコーデック - Azure
description: この記事では、StandardEncoderPreset で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f0cf89a755090010bb80507c52f90baa2a4e804a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617626"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standard Encoder の形式およびコーデック

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。 **StandardEncoderPreset** を使用してカスタム プリセットを作成する方法については、「[カスタム プリセットを使用して変換を作成する](customize-encoder-presets-how-to.md)」を参照してください。

## <a name="input-containerfile-formats"></a>入力コンテナー/ファイル形式

| ファイル形式 (ファイル拡張子) | サポートされています |
| --- | --- |
| (H.264 および AAC コーデックでの) FLV (.flv) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |Yes |
| Windows Media Video (WMV)/ASF (.wmv、.asf) |Yes |
| AVI (非圧縮 8-bit/10-bit) (.avi) |Yes |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |Yes |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |はい |

### <a name="audio-formats-in-input-containers"></a>入力コンテナーのオーディオ形式

Standard Encoder の入力コンテナーは、次のオーディオ形式に対応しています。

* オーディオ トラックにインターリーブ ステレオまたは 5.1 サンプルが含まれる MXF、GXF、QuickTime ファイル

または

* オーディオが個別 PCM トラックとして送信されるが、(ステレオまたは 5.1 への) チャネル マッピングをファイル メタデータから推測できる MXF、GXF、QuickTime ファイル

## <a name="input-video-codecs"></a>入力ビデオ コーデック
| 入力ビデオ コーデック | サポートされています |
| --- | --- |
| AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2 |
| Avid DNxHD (MXF) |はい |
| DVCPro/DVCProHD (MXF) |はい |
| デジタル ビデオ (DV) (AVI ファイルで) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル) |最大 422 プロファイル |
| MPEG-1 |はい |
| VC-1/WMV9 |はい |
| Canopus HQ/HQX |いいえ |
| MPEG-4 Part 2 |はい |
| [Theora](https://en.wikipedia.org/wiki/Theora) |はい |
| YUV420 非圧縮または中間 |Yes |
| Apple ProRes 422 |はい |
| Apple ProRes 422 LT |はい |
| Apple ProRes 422 HQ |はい |
| Apple ProRes プロキシ |はい |
| Apple ProRes 4444 |はい |
| Apple ProRes 4444 XQ |はい |
| HEVC/H.265| メイン プロファイル|

## <a name="input-audio-codecs"></a>入力オーディオ コーデック
| 入力オーディオ コーデック | サポートされています |
| --- | --- |
| AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1) |はい |
| MPEG Layer 2 |はい |
| MP3 (MPEG-1 Audio Layer 3) |はい |
| Windows Media オーディオ |はい |
| WAV/PCM |はい |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |はい |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |はい |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |はい |
| AMR (アダプティブ マルチ レート) |Yes |
| AES (SMPTE 331M および 302M、AES3-2003) |いいえ |
| Dolby® E |いいえ |
| Dolby® Digital (AC3) |いいえ |
| Dolby® Digital Plus (E-AC3) |いいえ |

## <a name="output-formats-and-codecs"></a>出力形式とコーデック
次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。

| ファイル形式 | ビデオ コーデック | オーディオ コーデック |
| --- | --- | --- |
| MP4 <br/><br/>(マルチビットレートの MP4 コンテナーを含む) |H.264 (High、Main、Baseline Profile)、HEVC (H.265) 8 ビット |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (High、Main、Baseline Profile) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>次の手順

[カスタム プリセットを使用して変換を作成する](customize-encoder-presets-how-to.md)
