---
title: センサー アクティビティを追跡する
description: イベント タイムラインには、アラートとアラート管理アクション、ネットワーク イベント、ユーザーのサインインやユーザーの削除などのユーザー操作を含む、ネットワークで検出されたアクティビティのタイムラインが表示されます。
ms.date: 12/10/2020
ms.topic: article
ms.openlocfilehash: 195908001fbd247ed2e0fa007bc8dcd5ebf28e60
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781621"
---
# <a name="track-sensor-activity"></a>センサー アクティビティを追跡する

## <a name="event-timeline"></a>イベント タイムライン

イベント タイムラインには、センサーによって検出されたアクティビティのタイムラインが表示されます。 例:

  - アラートとアラート管理のアクション

  - ネットワーク イベント

  - ユーザーのサインインやユーザーの削除などのユーザー操作

イベント タイムラインには、ネットワークで発生したイベントの時系列ビューがあります。 イベント タイムラインにより、攻撃やインシデントの前と後に発生したイベントの連鎖を把握して分析することができ、調査と科学捜査に役立ちます。

> [!NOTE]
> このセクションで説明する手順を実行できるのは、*管理者* と *セキュリティ アナリスト* です。

イベント ログを表示するには、次の操作を行います。

- サイド メニューから、 **[イベント タイムライン]** を選択します。

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="イベント タイムラインでイベントを表示します。":::

センサーによって検出されたイベントを表示するだけでなく、タイムラインにイベントを手動で追加することもできます。 このプロセスは、イベントが発生したのが外部システムであっても、自分のネットワークに影響を与える場合に便利です。重要なことは、イベントが記録され、タイムラインの一部として表示されることです。

イベントを手動で追加するには、次の操作を行います。

- **[イベントの作成]** を選択します。

イベント ログ情報を CSV ファイルにエクスポートするには、次の操作を行います。

- **[エクスポート]** を選択します。

## <a name="filter-the-event-timeline"></a>イベント タイムラインをフィルター処理する

目的のデバイスとイベントが表示されるように、タイムラインをフィルター処理します。

タイムラインをフィルター処理するには、次の操作を行います。

1. **[高度なフィルター]** を選択します。

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="[イベントの高度なフィルター] ウィンドウを使用して、イベントをフィルター処理します。":::

2. 次のようにイベント フィルターを設定します。

   - **[アドレスを含める]** :特定のデバイスのイベントを表示します。

   - **[アドレスを除外する]** :特定のデバイスのイベントを非表示にします。

   - **[イベントの種類を含める]** :特定のイベントの種類を表示します。

   - **[イベントの種類を除外する]** :特定のイベントの種類を非表示にします。

   - **[デバイス グループ]** :デバイス マップで定義されているデバイス グループを選択します。 このグループからのイベントのみが表示されます。

3. **[すべてクリア]** を選択して、選択したすべてのフィルターをクリアします。

4. **[アラートのみ]** 、 **[アラートと通知]** 、または **[すべてのイベント]** で検索します。

5. **[日付の選択]** を選択して、特定の日付を選択します。 日、時、分を選択します。 選択した時間枠内のイベントが表示されます。

6.  **[ユーザー操作]** を選択して、ユーザー操作イベントを追加または除外します。

7.  矢印 (**V**) を選択すると、イベントに関する詳細情報が表示されます。

    - 関連するアラート (存在する場合) を選択すると、アラートの詳細な説明が表示されます。

    - デバイスを選択すると、マップ上のデバイスが表示されます。

    - 関連するデバイスでフィルター処理する場合は、 **[関連デバイスでイベントをフィルター処理]** を選択します。

    - **[PCAP ファイル]** を選択すると、特定の時刻のネットワーク全体のパケット キャプチャが含まれている PCAP ファイル (存在する場合) がダウンロードされます。 
    
      PCAP ファイルには、イベントの正確なパラメーターを特定するのに役立つ技術情報が含まれています。 PCAP ファイルは、オープン ソースのアプリケーションである Wireshark などのネットワーク プロトコル アナライザーを使用して分析することができます。

## <a name="see-also"></a>関連項目

[アラートを表示する](how-to-view-alerts.md)
