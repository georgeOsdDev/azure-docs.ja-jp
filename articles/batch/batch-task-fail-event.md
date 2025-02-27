---
title: Azure Batch タスク失敗イベント
description: Batch のタスク失敗イベントのリファレンスです。 このイベントは、タスク完了イベントに加えて出力され、タスクが失敗したときの検出に使用できます。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91850814"
---
# <a name="task-fail-event"></a>タスク失敗イベント

 このイベントは、タスクがエラーで完了したときに出力されます。 現在は、すべてが 0 以外の終了コードはエラーと見なされます。 このイベントは、タスク完了イベント *に加えて* 出力され、タスクが失敗したときの検出に使用できます。


 次の例は、タスク失敗イベントの本文を示しています。

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|要素名|Type|Notes|
|------------------|----------|-----------|
|`jobId`|String|タスクを含むジョブの ID です。|
|`id`|String|タスクの ID です。|
|`taskType`|String|タスクの型です。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。 このイベントは、ジョブ準備タスク、ジョブ リリース タスク、または開始タスクでは発生しません。|
|`systemTaskVersion`|Int32|これは、タスクの内部再試行カウンターです。 Batch サービスは一時的問題に対応するタスクを内部で再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|`requiredSlots`|Int32|タスクを実行するために必要なスロット。|
|[`nodeInfo`](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[`multiInstanceSettings`](#multiInstanceSettings)|複合型|このタスクが複数のコンピューティング ノードが必要な複数インスタンス タスクであることを指定します。  詳細については、[`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) を参照してください。|
|[`constraints`](#constraints)|複合型|このタスクに適用される実行の制約。|
|[`executionInfo`](#executionInfo)|複合型|タスクの実行に関する情報が含まれます。|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|要素名|Type|Notes|
|------------------|----------|-----------|
|`poolId`|String|タスクが実行されたプールの ID。|
|`nodeId`|String|タスクが実行されたノードの ID。|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|要素名|Type|メモ|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|タスクに必要なコンピューター ノードの数。|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|要素名|Type|メモ|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|タスクを再試行できる最大回数。 Batch サービスは、終了コードが 0 以外の場合にタスクを再試行します。<br /><br /> この値によって再試行の回数が限定されますのでご注意ください。 Batch サービスはタスクを 1 回試行してから、上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch はタスクを最大 4 回試行します (初回試行 1 回と再試行 3 回)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|


###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|要素名|Type|メモ|
|------------------|----------|-----------|
|`startTime`|DateTime|タスクの実行が開始した時刻です。 '実行中' は **実行している** 状態に対応するため、タスクがリソース ファイルやアプリケーション パッケージを指定する場合、開始時刻はタスクがこれらのファイルやパッケージのダウンロードやデプロイを開始した時刻を反映します。  タスクが再起動または再実行された場合は、タスクが実行を開始したつい最近の時刻となります。|
|`endTime`|DateTime|タスクが完了した時間です。|
|`exitCode`|Int32|タスクの終了コード。|
|`retryCount`|Int32|Batch サービスによりタスクが再試行された回数。 タスクは、0 以外の終了コードで終了すると、指定された MaxTaskRetryCount まで再試行されます。|
|`requeueCount`|Int32|Batch サービスによりタスクがユーザー要求の結果として再度キューに入れられる回数です。<br /><br /> ユーザーが (プールをサイズ変更したり圧縮することで) プールからノードを削除するときやジョブを無効にしているとき、ユーザーはノードで実行中のタスクを再実行のキューに再度入れるよう指定できます。 この回数は、タスクがこうした理由により何回キューに入れられたかを追跡します。|
