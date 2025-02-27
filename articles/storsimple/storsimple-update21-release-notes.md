---
title: StorSimple 8000 シリーズ Update 2.2 リリース ノート | Microsoft Docs
description: StorSimple 8000 シリーズ Update 2.2 における新機能、問題、回避策について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956604"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 シリーズ Update 2.2 リリース ノート

## <a name="overview"></a>概要
次のリリース ノートでは、StorSimple 8000 シリーズの Update 2.2 の新機能について説明し、未解決の重要な問題を示します。 また、このリリースに含まれる StorSimple のソフトウェアの更新プログラムの一覧も含まれています。

Update 2.2 は、Release (GA) または Update 0.1 から Update 2.1 までを実行しているすべての StorSimple デバイスに適用できます。 Update 2.2 に関連付けられているデバイスのバージョンは、6.3.9600.17708 です。

StorSimple ソリューションの更新プログラムをデプロイする前に、リリース ノートに含まれる情報を確認してください。

> [!IMPORTANT]
> * Update 2.2 には、ソフトウェアのみの更新プログラムがあります。 この更新プログラムのインストールには 1.5 ～ 2 時間程度かかります。 
> * Update 2.1 を実行している場合は、できるだけ早く Update 2.2 を適用することをお勧めします。
> * 新しいリリースについては、更新プログラムの公開を段階的に行うため、更新プログラムが即座に表示されない場合があります。 これらの更新プログラムは間もなく利用可能になるため、数日待ってから、もう一度、更新プログラムの有無を確認してください。
> 
> 

## <a name="whats-new-in-update-22"></a>Update 2.2 の新機能
Update 2.2 では、主に次の点が強化されています。

* **領域回復の自動最適化** – 仮想プロビジョニングされたボリュームでデータが削除された場合は、未使用の記憶域ブロックを回復する必要があります。 このリリースでは、クラウドからの領域回復プロセスの強化により、以前のバージョンと比べて高速に未使用領域が使用可能になります。
* **スナップショットのパフォーマンスの向上** – Update 2.2 では、大規模なボリュームの使用中にデータの変化が最小または発生しない場合の特定のシナリオで、クラウド スナップショットの処理時間が向上しています。 この向上によりメリットを得られるシナリオには、アーカイブ ボリュームがあります。
* **サポート パッケージ収集の強化** – サポート パッケージの収集およびアップロード方法が、このリリースで改善されました。 
* **Update の信頼性の向上** – このリリースではバグの修正により、Update の信頼性が向上しています。

## <a name="issues-fixed-in-update-22"></a>Update 2.2 で修正された問題
次の表は、Update 2.2 および 2.1 で修正された問題の概要を示しています。    

| いいえ | 特徴量 | 問題 | 物理デバイスへの適用 | 仮想デバイスへの適用 |
| --- | --- | --- | --- | --- |
| 1 |ホストのパフォーマンス |以前のリリースでは、ローカル固定ボリュームの作成時や階層化ボリュームからローカル固定ボリュームへの変換中に、ホスト側のパフォーマンスの問題が発生していました。 今回のリリースではこの問題が修正され、ボリュームの作成時と変換時のホストのパフォーマンスが向上しています。 |はい |いいえ |
| 2 |ローカル固定ボリューム |まれに、ローカル固定ボリュームの作成時にシステムがクラッシュしていました。 このバグは今回のリリースで修正されています。 |はい |いいえ |
| 3 |階層化 |StorSimple Cloud Appliance (8010 および 8020) のメタデータのクラウドへの階層化で散発的なクラッシュが発生していました。 この問題は今回のリリースで修正されました。 |いいえ |はい |
| 4 |スナップショットの作成 |大規模なボリュームやデータの変化が最小または発生しない場合のシナリオで、増分スナップショットの作成に関する問題が発生していました。 これらの問題は今回のリリースで修正されました。 |はい |はい |
| 5 |Openstack 認証 |Openstack をクラウド サービス プロバイダーとして使用する場合、認証に関連するバグがたまに発生し、JSON のパーサーがクラッシュしていました。 このバグは今回のリリースで修正されました。 |はい |いいえ |
| 6 |ホスト側のコピー |以前のバージョンのソフトウェアでは、あるボリュームから別のボリュームにデータをコピーする際に、ODX のタイミングに関連するバグがたまに発生していました。 このため、コントローラーのフェールオーバーが発生し、システムが回復モードに入る可能性がありました。 このバグは今回のリリースで修正されました。 |はい |いいえ |
| 7 |Windows Management Instrumentation (WMI) |以前のバージョンのソフトウェアでは、例外 "\<ManagementException> プロバイダーの読み込みエラー" の Web プロキシ エラーが複数発生していました。 このバグは WMI メモリ リークに起因し、現在は修正されています。 |はい |いいえ |
| 8 |更新 |以前のバージョンのソフトウェアでは、特定のまれなケースで、更新プログラムをスキャンまたはインストールしようとすると "CisPowershellHcsscripterror" が発生しました。 この問題は今回のリリースで修正されました。 |はい |はい |
| 9 |サポート パッケージ |今回のリリースでは、サポート パッケージの収集およびアップロード方法が改善されています。 |はい |はい |

## <a name="known-issues-in-update-22"></a>Update 2.2 の既知の問題
次の表に、このリリースでの既知の問題の概要を示します。

| いいえ。 | 特徴量 | 問題 | コメント/回避策 | 物理デバイスへの適用 | 仮想デバイスへの適用 |
| --- | --- | --- | --- | --- | --- |
| 1 |ディスク クォーラム |まれに、8600 デバイスの EBOD エンクロージャに搭載されているディスクの大半が切断され、ディスク クォーラムが存在しない状況になり、記憶域プールがオフラインになるケースがあります。 ディスクを再接続してもオフラインの状態は変わりません。 |ユーザーは、デバイスを再起動する必要があります。 問題が解消しない場合は、Microsoft サポートに対処法をお問い合わせください。 |はい |いいえ |
| 2 |不正確なコントローラー ID |コントローラーの交換を実施すると、コントローラー 0 がコントローラー 1 として表示されることがあります。 コントローラーの交換中、ピア ノードからイメージが読み込まれるときに、コントローラー ID が最初、ピア コントローラーの ID として表示される場合があります。 まれなケースですが、この動作がシステムの再起動後に生じることもあります。 |ユーザーによる操作は不要です。 この状況は、コントローラーの交換が完了すると自然に解決します。 |はい |いいえ |
| 3 |ストレージ アカウント |Storage サービスを使用したストレージ アカウントの削除は、サポートされていないシナリオです。 これにより、ユーザー データを取得できなくなります。 | |はい |はい |
| 4 |デバイスのフェールオーバー |同じソース デバイスのボリューム コンテナーについて、複数のフェールオーバーを異なるターゲット デバイスに対して実行することはサポートされません。 1 つの障害発生デバイスから複数のデバイスにフェールオーバーすると、フェールオーバーされた最初のデバイス上のボリューム コンテナーがデータの所有権を失います。 このようなフェールオーバーが発生した後、これらのボリューム コンテナーを Azure クラシック ポータルで表示するとその表示や動作が変わります。 | |はい |いいえ |
| 5 |インストール |SharePoint 用 StorSimple アダプターのインストール中にインストールを正常に完了するためには、デバイスの IP を指定する必要があります。 | |はい |いいえ |
| 6 |Web プロキシ |Web プロキシ構成で指定プロトコルとして HTTPS を使用している場合、デバイスとサービス間の通信が影響を受け、デバイスがオフラインになります。 プロセスでサポート パッケージも生成され、デバイスで大量のリソースが使用されます。 |Web プロキシ URL で指定プロトコルとして HTTP を使用していることを確認してください。 詳細については、「 [デバイスの Web プロキシの構成](./storsimple-8000-configure-web-proxy.md)」を参照してください。 |はい |いいえ |
| 7 |Web プロキシ |登録されたデバイスで Web プロキシを構成して有効にする場合は、デバイス上でアクティブなコントローラーを再起動する必要があります。 | |はい |いいえ |
| 8 |クラウドの長い待機時間と高い I/O ワークロード |StorSimple デバイスで、非常に長いクラウドの待機時間 (秒単位) や高い I/O ワークロードの組み合わせが発生した場合、デバイス ボリュームはパフォーマンスが低下した状態になり、I/O は "デバイスの準備未完了" エラーで失敗します。 |デバイス コントローラーを手動で再起動するか、デバイスのフェールオーバーを実行して、この状況から復旧する必要があります。 |はい |いいえ |
| 9 |Azure PowerShell |新しい **VolumeContainer** オブジェクトを作成できるように、StorSimple のコマンドレット **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** を使用して最初のオブジェクトを選択すると、コマンドレットからすべてのオブジェクトが返されます。 |次のように、コマンドレットをかっこで囲みます。 **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |はい |はい |
| 10 |移行 |複数のボリューム コンテナーを渡して移行する場合、最新のバックアップの ETA は、最初のボリューム コンテナーに対してのみ正確です。 さらに、最初のボリューム コンテナー内の最初の 4 つのバックアップが移行された後に、移行が並列で開始されます。 |一度に 1 つのボリューム コンテナーを移行することをお勧めします。 |はい |いいえ |
| 11 |移行 |復元後、ボリュームはバックアップ ポリシーや仮想ディスク グループに追加されません。 |バックアップを作成するために、バックアップ ポリシーにこれらのボリュームを追加する必要があります。 |はい |はい |
| 12 |移行 |移行の完了後は、5000/7000 シリーズのデバイスから移行後のデータ コンテナーにアクセスできなくなります。 |移行が完了しコミットした後、移行後のデータ コンテナーを削除することをお勧めします。 |はい |いいえ |
| 13 |複製と障害復旧 |Update 1 を実行する StorSimple デバイスでは、Update 1 よりも前のソフトウェアを実行するデバイスへの複製とディザスター リカバリーは実行できません。 |これらの操作を実行できるようにするには、ターゲット デバイスを Update 1 に更新する必要があります。 |はい |はい |
| 14 |移行 |ボリュームが関連付けられていないボリューム グループがある場合、5000/7000 シリーズ デバイスで移行のための構成バックアップが失敗する可能性があります。 |ボリュームが関連付けられていない空のボリューム グループをすべて削除してから、構成バックアップをやり直してください。 |はい |いいえ |
| 15 |Azure PowerShell コマンドレットとローカル固定ボリューム |Azure PowerShell コマンドレットを使用してローカル固定ボリュームを作成することはできません。 (Azure PowerShell を使用して作成するすべてのボリュームは階層化されます。) |ローカル固定ボリュームを構成するには、常に StorSimple Manager サービスを使用してください。 |はい |いいえ |
| 16 |ローカル固定ボリュームで使用可能な領域 |ローカル固定ボリュームを削除する場合、新しいボリュームで使用可能な領域がすぐに更新されない可能性があります。 StorSimple Manager サービスは、ローカルで使用可能な領域を約 1 時間ごとに更新します。 |新しいボリュームを作成しようとする前に、1 時間待機してください。 |はい |いいえ |
| 17 |ローカル固定ボリューム |復元ジョブで、スナップショットの一時的なバックアップが、復元ジョブの期間中にのみバックアップ カタログに公開されます。 さらに、復元ジョブの実行中にのみ、 **[バックアップ ポリシー]** ページにプレフィックス **tmpCollection** を持つ仮想ディスク グループが公開されます。 |この動作は、復元ジョブにローカル固定ボリュームのみが存在する場合、またはローカル固定ボリュームと階層化されたボリュームが混在する場合に発生することがあります。 復元ジョブに階層化されたボリュームのみが含まれている場合、この動作は発生しません。 ユーザーが操作を行う必要はありません。 |はい |いいえ |
| 18 |ローカル固定ボリューム |復元ジョブをキャンセルし、その直後にコントローラーのフェールオーバーが発生した場合、復元ジョブには **Canceled** ではなく **Failed** と表示されます。 復元ジョブが失敗し、その直後にコントローラーのフェールオーバーが発生した場合、復元ジョブには **Failed** ではなく **Canceled** と表示されます。 |この動作は、復元ジョブにローカル固定ボリュームのみが存在する場合、またはローカル固定ボリュームと階層化されたボリュームが混在する場合に発生することがあります。 復元ジョブに階層化されたボリュームのみが含まれている場合、この動作は発生しません。 ユーザーが操作を行う必要はありません。 |はい |いいえ |
| 19 |ローカル固定ボリューム |復元ジョブをキャンセルまたは復元が失敗し、その後にコントローラーのフェールオーバーが発生した場合、 **[ジョブ]** ページに追加の復元ジョブが表示されます。 |この動作は、復元ジョブにローカル固定ボリュームのみが存在する場合、またはローカル固定ボリュームと階層化されたボリュームが混在する場合に発生することがあります。 復元ジョブに階層化されたボリュームのみが含まれている場合、この動作は発生しません。 ユーザーが操作を行う必要はありません。 |はい |いいえ |
| 20 |ローカル固定ボリューム |(Update 1.2 以降で作成および複製された) 階層化ボリュームをローカル固定ボリュームに変換しようとして、デバイスの容量が不足していた場合、またはクラウドが停止していた場合は、クローンが破損する可能性があります。 |この問題は、Update 2.1 より前のソフトウェアで作成および複製されたボリュームでのみ発生します。 このようなことはあまり起きないはずです。 | | |
| 21 |ボリュームの変換 |ボリュームの変換 (階層化からローカル固定に、またはその逆に) が行われている間は、ボリュームに接続された ACR を更新しないでください。 ACR を更新すると、データが破損する可能性があります。 |必要な場合は、ボリューム変換の前に ACR を更新し、変換の進行中はそれ以上 ACR の更新を行わないでください。 | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Update 2.2 のコントローラーとファームウェアの更新プログラム
このリリースには、ソフトウェアのみの更新プログラムがあります。 ただし、Update 2 より前のバージョンから更新する場合は、ドライバー、Storport、Spaceport、(場合によっては) ディスク ファームウェアの更新プログラムをデバイスにインストールする必要があります。

ドライバー、Storport、Spaceport、ディスク ファームウェアの更新プログラムをインストールする方法の詳細については、StorSimple デバイス上の [Update 2.2 のインストール](./storsimple-8000-install-update-5.md) に関する記事をご覧ください。

## <a name="virtual-device-updates-in-update-22"></a>Update 2.2 での仮想デバイスの更新
この更新プログラムは、仮想デバイスには適用できません。 新しい仮想デバイスを作成する必要があります。 

## <a name="next-step"></a>次のステップ
StorSimple デバイスに [Update 2.2 をインストールする](./storsimple-8000-install-update-5.md) 方法を確認します。