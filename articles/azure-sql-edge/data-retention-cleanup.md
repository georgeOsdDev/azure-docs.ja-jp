---
title: アイテム保持ポリシーを使用して履歴データを管理する - Azure SQL Edge
description: Azure SQL Edge でアイテム保持ポリシーを使用して履歴データを管理する方法について説明します
keywords: SQL Edge, データ保有期間
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392369"
---
# <a name="manage-historical-data-with-retention-policy"></a>アイテム保持ポリシーを使用して履歴データを管理する

データ保有期間は、データベースと基になる任意のテーブルで個別に有効にすることができます。これにより、ユーザーは自分のテーブルおよびデータベースに対して柔軟なエージング ポリシーを作成できます。 データ保有期間は簡単に適用できます。必要なのは、テーブル作成時またはテーブル変更操作の一部としてパラメーターを 1 つ設定することだけです。 

データベースと基になるテーブルに対してデータ保持ポリシーを定義した後、バックグラウンド時間タイマー タスクを実行して、データ保有期間が有効になっているテーブルから古いレコードを削除します。 一致する行の識別とテーブルからの削除は、システムによってスケジュール設定されて実行されるバックグラウンド タスクにおいて透過的に行われます。 テーブルの行の Age 条件は、テーブル定義の `filter_column` として使用されている列に基づいてチェックされます。 たとえば、保持期間が 1 週間に設定されている場合、クリーンアップの対象となるテーブルの行は次のいずれかの条件を満たします。 

- フィルター列に DATETIMEOFFSET データ型が使用されている場合、条件は `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())` となります。
- それ以外の場合、条件は `filter_column < DATEADD(WEEK, -1, SYSDATETIME())` となります。

## <a name="data-retention-cleanup-phases"></a>データ保有のクリーンアップ フェーズ

データ保有のクリーンアップ操作は、2 つのフェーズで構成されます。 
- 検出フェーズ - このフェーズでは、クリーンアップ操作によって、ユーザー データベース内のすべてのテーブルが特定され、クリーンアップの一覧が作成されます。 検出は 1 日に 1 回実行されます。
- クリーンアップ フェーズ - このフェーズでは、検出フェーズで特定されたデータ保持の期間が有限であるすべてのテーブルに対してクリーンアップを実行します。 テーブルに対してクリーンアップ操作を実行できない場合、そのテーブルは現在の実行ではスキップされ、次の繰り返しで再試行されます。 クリーンアップ時には、次の原則が使用されます。
    - 古い行が別のトランザクションによってロックされている場合、その行はスキップされます。 
    - 既定の 5 秒のロック タイムアウト設定を使用して、クリーンアップを実行します。 タイムアウト ウィンドウ内にテーブルのロックを取得できない場合、現在の実行ではテーブルがスキップされ、次の繰り返しで再試行されます。
    - テーブルのクリーンアップ中にエラーが発生した場合、そのテーブルはスキップされ、次の繰り返しで選択されます。

## <a name="manual-cleanup"></a>手動クリーンアップ

テーブルのデータ保有期間の設定とデータベースのワークロードの性質によっては、自動クリーンアップ スレッドの実行中にすべての古い行を完全に削除できない可能性があります。 これをサポートし、ユーザーが古い行を手動で削除できるようにするため、Azure SQL Edge で `sys.sp_cleanup_data_retention` ストアド プロシージャが導入されました。 

このストアド プロシージャは、3 つのパラメーターを受け取ります。 
    - スキーマ名 - テーブルを所有するスキーマの名前。 これは必須パラメーターです。 
    - テーブル名 - 手動クリーンアップを実行するテーブルの名前。 これは必須パラメーターです。 
    - 行数 (出力) - 出力変数。 手動クリーンアップ sp によってクリーンアップされた行の数を返します。 これは省略可能なパラメーターです。 

次の例は、テーブル `dbo.data_retention_table` の手動クリーンアップ sp の実行を示しています。

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>古い行の削除方法

クリーンアップ プロセスは、テーブルのインデックスのレイアウトに依存します。 有限の保持期間を持つすべてのテーブルの古いデータをクリーンアップするために、バックグラウンド タスクが作成されます。 行ストア (B ツリーまたはヒープ) のインデックスのクリーンアップ ロジックでは、よりサイズの小さなまとまり (最大 10K) で期限切れの行が削除され、データベース ログや IO サブシステムへの負荷が軽減されます。 クリーンアップ ロジックは必要な B ツリー インデックスを利用しますが、保有期間より古い行の削除の順序は確実には保証できません。 そのため、"アプリケーションではクリーンアップ順序に依存しないでください"。

クラスター化列ストアのクリーンアップ タスクでは、行グループ全体を一度に削除します (通常、各グループには 100 万行が含まれます)。これは非常に効率的であり、データが速いペースで生成および期限切れになるときは特にそうです。

![データ保有のクリーンアップ](./media/data-retention-cleanup/data-retention-cleanup.png)

優れたデータ圧縮と効率的な保有期間のクリーンアップにより、クラスター化列ストア インデックスはワークロードが急速に大量のデータを生成するシナリオに最適な選択肢になります。

> [!Note]
> B ツリーのインデックスとヒープの場合、データ保有は基になるテーブルに対して削除クエリを実行しますが、テーブルの削除トリガーと競合する可能性があります。 削除トリガーをテーブルから削除するか、削除 DML トリガーが設定されたテーブルではデータ保持期間を有効にしないことを推奨します。

## <a name="monitoring-data-retention-cleanup"></a>データ保有のクリーンアップの監視

データ保持ポリシーのクリーンアップ操作は、Azure SQL Edge の拡張イベント (Xevent) を使用して監視できます。 拡張イベントの詳細については、[Xevent の概要](/sql/relational-databases/extended-events/extended-events)に関するページを参照してください。 

次の 6 つの拡張イベントは、クリーンアップ操作の状態を追跡するのに役立ちます。 

| 名前 | 説明 |
|------| ------------|
| data_retention_task_started  | アイテム保持ポリシーを伴うテーブルのクリーンアップのためのバックグラウンド タスクが開始したときに発生します。 |
| data_retention_task_completed  | アイテム保持ポリシーを伴うテーブルのクリーンアップのためのバックグラウンド タスクが終了したときに発生します。 |
| data_retention_task_exception  | アイテム保持ポリシーを伴うテーブルのクリーンアップのためのバックグラウンド タスクが、テーブルに固有の保有クリーンアップ処理の外で失敗したときに発生します。 |
| data_retention_cleanup_started  | データ保持ポリシーを伴うテーブルのクリーンアップ処理が開始したときに発生します。 |
| data_retention_cleanup_exception  | アイテム保持ポリシーを伴うテーブルのクリーンアップ処理が失敗したときに発生します。 |
| data_retention_cleanup_completed  | データ保持ポリシーを伴うテーブルのクリーンアップ処理が終了したときに発生します。 |  

さらに、`RING_BUFFER_DATA_RETENTION_CLEANUP` という名前の新しい種類のリング バッファーが sys.dm_os_ring_buffers 動的管理ビューに追加されました。 このビューは、データ保有のクリーンアップ操作を監視するために使用できます。 


## <a name="next-steps"></a>次の手順
- [データ保持ポリシー](data-retention-overview.md)
- [データ保持ポリシーを有効または無効にする](data-retention-enable-disable.md)