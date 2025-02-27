---
title: Azure Site Recovery を使用して VMware VM をオンプレミス サイトに再保護する
description: Azure Site Recovery を使用して Azure にフェールオーバーした後で、VMware VM を再保護する方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89441495"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Azure からオンプレミスへの再保護

オンプレミスの VMware VM または物理サーバーを Azure に[フェールオーバーした](site-recovery-failover.md)後、オンプレミス サイトにフェールバックする最初の手順は、フェールオーバー時に作成された Azure VM を再保護することです。 この記事では、その方法について説明します。 

## <a name="before-you-begin"></a>開始する前に

1. [こちらの記事](vmware-azure-prepare-failback.md)の手順に従って、再保護とフェールバックを準備します。これには、Azure のプロセス サーバーおよびオンプレミスのマスター ターゲット サーバーのセットアップや、フェールバックのためのサイト間 VPN または ExpressRoute プライベート ピアリングの構成が含まれます。
2. オンプレミスの構成サーバーが実行され、Azure に接続されていることを確認してください。 Azure へのフェールオーバー中は、オンプレミス サイトにアクセスできない可能性があるため、構成サーバーは使用できないか、またはシャットダウンされることがあります。 フェールバック時は、VM が構成サーバー データベースに存在している必要があります。 それ以外の場合、フェールバックは失敗します。
3. オンプレミスのマスター ターゲット サーバー上のスナップショットをすべて削除してください。 スナップショットが存在すると、再保護は機能しません。  VM 上のスナップショットは、再保護ジョブ中に自動的にマージされます。
4. マルチ VM 整合性のためにレプリケーション グループに収集された VM を再保護している場合は、それらがすべて同じオペレーティング システム (Windows または Linux) を使用していること、およびデプロイするマスター ターゲット サーバーが同じ種類のオペレーティング システムを使用していることを確認してください。 レプリケーション グループ内の VM では、すべて同じマスター ターゲット サーバーを使用する必要があります。
5. フェールバックのために[必要なポート](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)を開きます。
6. フェールバックの前に、vCenter Server が接続されていることを確認してください。 接続されていない場合、ディスクの切断と仮想マシンへの再接続は失敗します。
7. vCenter サーバーによってフェールバック先の VM が管理されている場合は、必要なアクセス許可があることを確認してください。 読み取り専用ユーザー vCenter の検出を実行し、仮想マシンを保護すると、適切に保護され、フェールオーバーが機能します。 ただし、再保護中は、データストアを検出できず、データストアは一覧表示されないため、フェールオーバーは失敗します。 この問題を解決するには、[適切なアカウント/アクセス許可](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)を使用して vCenter 資格情報を更新してから、ジョブを再試行できます。 
8. テンプレートを使用して仮想マシンを作成した場合は、各 VM のディスクに独自の UUID が割り当てられていることを確認してください。 オンプレミス VM の UUID とマスター ターゲット サーバーの UUID が、どちらも同じテンプレートから作成されたために衝突している場合、再保護は失敗します。 異なるテンプレートからデプロイしてください。
9. 別の vCenter サーバーにフェールバックする場合は、新しい vCenter サーバーとマスター ターゲット サーバーが検出されることを確認してください。 通常は、これらがデータストアではないか、アクセスできないか、または **[再保護]** に表示されない場合です。
10. フェールバックできない次のシナリオを確認してください。
    - ESXi 5.5 無料エディションまたは vSphere 6 Hypervisor 無料エディションのどちらかを使用している場合。 別のバージョンにアップグレードしてください。
    - Windows Server 2008 R2 SP1 物理サーバーを使用している場合。
    - VMware VM を Hyper-V にフェールバックすることはできません。
    - 移行されている VM。
    - 別のリソース グループに移動されている VM。
    - 削除されているレプリカ Azure VM。
    - 保護されていない (オンプレミス サイトにレプリケートされている) レプリカ Azure VM。
10. 使用できる[フェールバックの種類 (元の場所への復旧と別の場所への復旧)](concepts-types-of-failback.md) を確認してください。


## <a name="enable-reprotection"></a>再保護を有効にする

レプリケーションを有効にします。 特定の VM または復旧計画を再保護することができます。

- 復旧計画を再保護する場合は、すべての保護されたマシンの値を指定する必要があります。
- VM がマルチ VM 整合性用のレプリケーション グループに属している場合は、復旧計画を使用してのみ再保護できます。 レプリケーション グループ内の VM は、同じマスター ターゲット サーバーを使用する必要があります。

>[!NOTE]
>再保護中に Azure から以前のソースに送信されるデータの量は、0 バイトから保護されているすべてのマシンのディスク サイズの合計までの間の任意の値になる可能性があり、計算することはできません。

### <a name="before-you-start"></a>開始する前に

- フェールオーバーの後で Azure 上で VM が起動した後、エージェントが構成サーバーに再度登録されるまでにある程度の時間 (最大 15 分) かかります。 この間は再保護を実行できないため、エージェントがインストールされていないというエラー メッセージが表示されます。 これが発生した場合は、数分待ってから再保護を実行してください。
- Azure VM を既存のオンプレミスの VM にフェールバックする場合は、マスター ターゲット サーバーの ESXi ホスト上で読み取り/書き込みアクセス権のあるオンプレミスの VM のデータストアをマウントします。
- オンプレミスの VM が存在しない場合などに別の場所にフェールオーバーする場合は、マスター ターゲット サーバー用に構成されているリテンション ドライブとデータストアを選択します。 オンプレミス サイトにフェールバックした場合、フェールバック保護計画の VMware 仮想マシンはマスター ターゲット サーバーと同じデータストアを使用します。 その後、新しい VM が vCenter に作成されます。

次のように再保護を有効にします。

1. **[コンテナー]**  >  **[レプリケートされたアイテム]** の順に選択します。 フェールオーバーされた仮想マシンを右クリックし、 **[再保護]** を選択します。 または、コマンド ボタンからマシンを選択し、 **[再保護]** を選択します。
2. 保護の方向として **[Azure からオンプレミスへ]** が選択されていることを確認します。
3. **[マスター ターゲット サーバー]** と **[プロセス サーバー]** で、オンプレミスのマスター ターゲット サーバーとプロセス サーバーを選択します。  
4. **[Datastore] \(データストア)** では、オンプレミスのディスクの復旧先のデータストアを選択します。 このオプションは、オンプレミスの仮想マシンを削除し、新しいディスクを作成する必要がある場合に使用します。 ディスクが既に存在する場合、このオプションは無視されます。 それでも値は指定しておく必要があります。
5. リテンション ドライブを選択します。
6. フェールバック ポリシーは自動的に選択されます。
7. **[OK]** を選択して再保護を開始します。

    ![[再保護] ダイアログ ボックス](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Azure VM をオンプレミス サイトにレプリケートするジョブが開始されます。 進行状況は **[ジョブ]** タブで追跡できます。
    - 再保護ジョブが成功すると、VM は保護された状態になります。
    - 再保護中は、オンプレミス VM を無効にします。 これは、レプリケーション中のデータの整合性を確保するのに役立ちます。
    - 再保護が完了した後は、オンプレミスの VM を有効にしないでください。
   

## <a name="next-steps"></a>次のステップ

- 問題が発生した場合は、[トラブルシューティングに関する記事](vmware-azure-troubleshoot-failback-reprotect.md)を参照してください。
- Azure VM が保護された後、[フェールバックを実行](vmware-azure-failback.md)できます。 フェールバックによって Azure VM がシャットダウンされ、オンプレミスの VM が起動されます。 アプリケーションのダウンタイムを想定し、それに応じてフェールバック時間を選択してください。


