---
title: StorSimple 8000 シリーズ デバイス コントローラーを管理する | Microsoft Docs
description: StorSimple デバイス コントローラーの停止、再起動、シャットダウン、リセットの方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956995"
---
# <a name="manage-your-storsimple-device-controllers"></a>StorSimple デバイス コントローラーを管理する

## <a name="overview"></a>概要

このチュートリアルでは、StorSimple デバイス コントローラーに対して実行できる各種の操作について説明します。 StorSimple デバイスでは、ピア コントローラーがアクティブ/パッシブ構成で冗長化されています。 特定の時点でアクティブなコントローラーは 1 台だけで、そのコントローラーがすべてのディスク操作とネットワーク操作を処理します。 その他のコントローラーはパッシブ モードになります。 アクティブ コントローラーに障害が発生すると、パッシブ コントローラーが自動的にアクティブになります。

このチュートリアルでは、以下の手段を使ってデバイス コントローラーを管理するための手順を取り上げています。

* StorSimple デバイス マネージャー サービスの目的のデバイス用の **[コントローラー]** ブレード。
* StorSimple 用 Windows PowerShell。

デバイス コントローラーは、StorSimple デバイス マネージャー サービスを介して管理することをお勧めします。 StorSimple 用 Windows PowerShell を使用しないと実行できない操作については、チュートリアルの中で記載しています。

このチュートリアルを読むと、次のことができるようになります。

* StorSimple デバイス コントローラーを再起動またはシャットダウンする
* StorSimple デバイスをシャットダウンする
* StorSimple デバイスを出荷時の設定にリセットする

## <a name="restart-or-shut-down-a-single-controller"></a>単一のコントローラーの再起動とシャットダウン
通常のシステム運用の中でコントローラーの再起動またはシャットダウンが必要になることはありません。 単一デバイス コントローラーのシャットダウン操作が一般的に行われるのは、障害の発生したデバイスのハードウェア コンポーネントを交換しなければならないケースです。 また、過剰なメモリの使用やコントローラーの不調などでパフォーマンスに影響が生じている状況でコントローラーの再起動が必要となる場合もあります。 無事交換したコントローラーを有効にしてテストする場合にも、コントローラーを再起動する必要があります。

デバイスを再起動しても、パッシブ コントローラーが利用できる状態にあれば、接続されているイニシエーターに悪影響は生じません。 パッシブ コントローラーが利用できない、またはオフになっている場合は、アクティブ コントローラーを再起動すると、サービスの機能不全やダウンタイムが生じます。

> [!IMPORTANT]
> * **実行中のコントローラーを物理的に取り外すことは絶対に避けてください。冗長性が失われ、ダウンタイムのリスクが増大します。**
> * 以下の手順は、StorSimple の物理デバイスにのみ当てはまります。 StorSimple Cloud Appliance の起動、停止、および再起動については、[クラウド アプライアンスの操作](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance)に関するページをご覧ください。

StorSimple デバイス マネージャー サービスの Azure ポータルまたは StorSimple 用 Windows PowerShell を使用して、1 つのデバイス コントローラーを再起動またはシャットダウンできます。

Azure ポータルからデバイス コントローラーを管理するには、次の手順を実行します。

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Azure ポータルでコントローラーを再起動またはシャットダウンするには
1. StorSimple デバイス マネージャー サービスで、**[デバイス]** に移動します。 デバイスの一覧からデバイスを選択します。 

    ![デバイスを選択する](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. **[設定]、[コントローラー]** の順に移動します。
   
    ![StorSimple デバイス コントローラーが正常であることを確認する](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. **[コントローラー]** ブレードで、デバイスの両方のコントローラーの状態が **[正常]** であることを確認します。 コントローラーを選択します。右クリックした後、**[再起動]** または **[シャットダウン]** を選択します。

    ![StorSimple デバイス コントローラーの再起動またはシャットダウンを選択する](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. コントローラーを再起動またはシャット ダウンするジョブが作成され、該当する警告が存在する場合は、警告が表示されます。 再起動またはシャットダウンを監視するには、**[サービス]、[アクティビティ ログ]** の順に移動し、サービスに固有のパラメーターでフィルター処理します。 コントローラーがシャット ダウンされた場合は、電源ボタンを押してコントローラーをオンにする必要があります。

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でコントローラーを再起動またはシャットダウンするには
StorSimple デバイスの単一のコントローラーを StorSimple 用 Windows PowerShell でシャットダウンまたは再起動するには、次の手順を実行します。

1. リモート コンピューターからシリアル コンソールまたは Telnet セッション経由でデバイスにアクセスします。 コントローラー 0 またはコントローラー 1 に接続するには、「[PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)」の手順に従います。
2. シリアル コンソール メニューで、オプション 1 を選択して、**フル アクセスでログイン** します。
3. バナー メッセージで、接続先のコントローラーがどれであるか (コントローラー 0 またはコントローラー 1)、また、それがアクティブ コントローラーであるかパッシブ (スタンバイ) コントローラーであるかを書き留めます。
   
   * 単一のコントローラーをシャットダウンするには、プロンプトで次のように入力します。
     
       `Stop-HcsController`
     
       これで接続先のコントローラーがシャットダウンされます。 アクティブ コントローラーを停止した場合、デバイスはパッシブ コントローラーにフェールオーバーされます。

   * コントローラーを再起動するには、プロンプトで次のように入力します。
     
       `Restart-HcsController`
     
       これで接続先のコントローラーが再起動されます。 アクティブ コントローラーを再起動した場合は、パッシブ コントローラーにフェールオーバーした後で再起動されます。

## <a name="shut-down-a-storsimple-device"></a>StorSimple デバイスをシャットダウンする

このセクションでは、実行中の StorSimple デバイスまたは障害の発生した StorSimple デバイスをリモート コンピューターからシャットダウンする方法について説明します。 両方のデバイス コントローラーをシャットダウンすると、デバイスはオフになります。 デバイスのシャットダウンは、そのデバイスを物理的に移動するか、または運用から外すときに行います。

> [!IMPORTANT]
> デバイスをシャットダウンする前に、デバイスのコンポーネントの正常性を確認してください。 デバイスに移動し、**[設定]、[ハードウェアの正常性]** の順にクリックします。 **[状態とハードウェアの正常性]** ブレードで、すべてのコンポーネントの LED ステータスが緑色になっていることを確認します。 正常なデバイスのみ、緑色のステータスになります。 デバイスをシャットダウンする場合や、不調なコンポーネントを交換する場合、コンポーネントごとにエラー (赤色) または機能低下 (黄色) ステータスが表示されます。


#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple デバイスをシャットダウンするには

1. [コントローラーの再起動とシャットダウン](#restart-or-shut-down-a-single-controller) の手順に従って、デバイス上のパッシブ コントローラーを特定し、シャットダウンします。 この操作は、Azure ポータルまたは StorSimple 用 Windows PowerShell で実行できます。
2. 上の手順を繰り返してアクティブ コントローラーをシャットダウンします。
3. ここでデバイスのバックプレーンを確認する必要があります。 2 つのコントローラーが完全にシャットダウンされた後、両方のコントローラーのステータス LED が赤色に点滅しています。 この時点でデバイスの電源を完全にオフにする必要がある場合は、両方の電源/冷却モジュール (PCM) の電源スイッチをオフの位置に設定してください。 これでデバイスの電源が切れます。

## <a name="reset-the-device-to-factory-default-settings"></a>デバイスを出荷時の設定にリセットする

> [!IMPORTANT]
> デバイスを出荷時の設定にリセットする必要がある場合は、Microsoft サポートにお問い合わせください。 次に示す手順は、Microsoft サポートと共に実行する場合にのみ使用してください。

ここでは、StorSimple 用 Windows PowerShell を使用して、Microsoft Azure StorSimple デバイスを出荷時の既定の設定にリセットする方法について説明します。
デバイスをリセットすると、既定でクラスター全体からすべてのデータと設定が削除されます。

Microsoft Azure StorSimple デバイスを出荷時の設定にリセットするには、次の手順に従います。

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell でデバイスを既定の設定にリセットするには
1. シリアル コンソールを使ってデバイスにアクセスします。 **アクティブ** コントローラーに接続されていることをバナー メッセージで確認してください。
2. シリアル コンソール メニューで、オプション 1 を選択して、**フル アクセスでログイン** します。
3. プロンプトで次のコマンドを入力して、クラスター全体をリセットし、すべてのデータ、メタデータ、コントローラー設定を削除します。
   
    `Reset-HcsFactoryDefault`
   
    代わりに、1 つのコントローラーだけをリセットするには、`-scope` パラメーターを指定して [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) コマンドレットを使用します。
   
    システムが何度か再起動されます。 リセットが正常に完了すると、その旨通知されます。 このプロセスの所要時間はシステムのモデルによって異なります。8100 デバイスの場合は 45 ～ 60 分、8600 の場合は 60 ～ 90 分かかります。
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>デバイス コントローラーの管理に関する FAQ
このセクションでは、StorSimple デバイス コントローラーの管理に関して、よく寄せられる質問とその答えを掲載しています。

**Q.**  デバイスの両方のコントローラーが正常で電源が投入されているときに、アクティブ コントローラーを再起動またはシャットダウンした場合、どうなりますか。

**A.** デバイスの両方のコントローラーが正常で電源が投入されている場合、確認のメッセージが表示されます。 次の操作を選択できます。

* **アクティブ コントローラーを再起動する** – アクティブ コントローラーを再起動するとデバイスがパッシブ コントローラーにフェールオーバーされる、という内容の通知が表示されます。 コントローラーが再起動します。
* **アクティブ コントローラーをシャットダウンする** – アクティブ コントローラーをシャットダウンするとダウンタイムが発生する、という内容の通知が表示されます。 この場合、コントローラーをオンにするには、デバイスの電源ボタンを押す必要があります。

**Q.**  デバイスのパッシブ コントローラーが利用できない、または電源が切れているときに、アクティブ コントローラーを再起動またはシャットダウンした場合はどうなりますか。

**A.**  デバイスのパッシブ コントローラーが利用できない、または電源が切れている場合、次の操作を選択できます。

* **アクティブ コントローラーを再起動する** – 操作を続行すると一時的にサービスが中断される、という内容の通知が表示され、確認を求められます。
* **アクティブ コントローラーをシャットダウンする** – 操作を続行するとダウンタイムが発生する、という内容の通知が表示されます。 この場合、コントローラーをオンにするには、デバイスの電源ボタンを押す必要があります。 確認を求められます。

**Q.**  コントローラーの再起動またはシャットダウンに失敗するのはどのようなときでしょうか。

**A.**  次の場合、コントローラーの再起動またはシャットダウンに失敗します。

* デバイスの更新が進行中であるとき。
* コントローラーの再起動が既に進行中であるとき。
* コントローラーのシャットダウンが既に進行中であるとき。

**Q.**  コントローラーが再起動されたかどうかやシャットダウンされたかどうかは、どのようにして確認できるでしょうか。

**A.** コントローラーの状態は、[コントローラー] ブレードで確認できます。 コントローラーの状態は、コントローラーが再起動またはシャット ダウンの処理中であるかどうかを示します。 コントローラーが再起動またはシャットダウンされた場合、**[アラート]** ブレードにも情報アラートが表示されます。 アクティブ ログにも、コントローラーの再起動操作とシャットダウン操作が記録されます。 アクティビティ ログの詳細については、「[アクティビティ ログの表示](storsimple-8000-service-dashboard.md#view-the-activity-logs)」を参照してください。

**Q.**  コントローラーのフェールオーバーは I/O に影響しますか。

**A.**  コントローラーがフェールオーバーされるとイニシエーターとアクティブ コントローラー間の TCP 接続がリセットされますが、パッシブ コントローラーが操作を引き継ぐときに、接続が再度確立されます。 この操作の過程でイニシエーターとデバイス間の I/O アクティビティが一時的 (30 秒未満) に停止する場合があります。

**Q.**  シャットダウンして運用から除外したコントローラーを再び運用状態に戻すにはどうすればよいでしょうか。

**A.** コントローラーを運用状態に戻すには、「 [StorSimple デバイスのコントローラー モジュールを交換する](storsimple-8000-controller-replacement.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* このチュートリアルで紹介した手順では解決できないような問題が StorSimple デバイス コントローラーで発生した場合は、 [Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md)ください。
* StorSimple デバイス マネージャー サービスの使用の詳細については、「[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)」(StorSimple デバイス マネージャーを使用して StorSimple デバイスを管理する) を参照してください。