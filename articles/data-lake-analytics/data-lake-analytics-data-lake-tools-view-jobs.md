---
title: ジョブ ブラウザーとジョブ ビューを使用する - Azure Data Lake Analytics
description: この記事では、Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用方法について説明します。
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/02/2017
ms.openlocfilehash: a1e9a9df4c2ec57dfeec8cf5ddd5348228b9cc3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018564"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics ジョブに対してジョブ ブラウザーとジョブ ビューを使用
Azure Data Lake Analytics サービスは、送信されたジョブをクエリ ストアにアーカイブします。 この記事では、Azure Data Lake Tools for Visual Studio のジョブ ブラウザーとジョブ ビューを使用して過去のジョブ情報を検索する方法について説明します。 

Data Lake Analytics サービスは、既定で 30 日間ジョブをアーカイブします。 この有効期限は、Azure Portal で、カスタマイズした有効期限ポリシーを構成することで構成できます。 有効期限が切れた後はジョブ情報にアクセスできなくなります。 

## <a name="prerequisites"></a>前提条件
[Azure Data Lake Tools for Visual Studio の前提条件](data-lake-analytics-data-lake-tools-get-started.md#prerequisites)を参照してください。

## <a name="open-the-job-browser"></a>ジョブ ブラウザーを開く
Visual Studio で **[サーバー エクスプローラー]、[Azure]、[Data Lake Analytics]、[ジョブ]** の順に選択して、ジョブ ブラウザーにアクセスします。  このジョブ ブラウザーを使用して、Data Lake Analytics アカウントのクエリ ストアにアクセスできます。 ジョブ ブラウザーには、ジョブの基本情報を示すクエリ ストアが左側に表示され、ジョブの詳細情報を示すジョブ ビューが右側に表示されます。

## <a name="job-view"></a>ジョブ ビュー
ジョブ ビューには、特定のジョブの詳細な情報が示されます。 ジョブを開くには、ジョブ ブラウザーでジョブをダブルクリックするか、Data Lake メニューの [Job View (ジョブ ビュー)] をクリックします。 ジョブの URL が設定されたダイアログが表示されます。

![Data Lake Tools for Visual Studio のジョブ ブラウザー](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

ジョブ ビューには、次の内容が含まれています。

* ジョブの概要
  
    実行中のジョブの最新情報を表示するには、ジョブ ビューを最新の情報に更新します。
  
  * ジョブの状態 (グラフ):
    
      ジョブの状態は、ジョブのフェーズを表します。
    
      ![Azure Data Lake Analytics ジョブのフェーズを示すスクリーンショット。](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * 準備中:スクリプトをクラウドにアップロードし、コンパイル サービスを使用してスクリプトのコンパイルと最適化を行っています。
    * キューに挿入済み:十分なリソースが確保されるまで待機しているか、ジョブの数がアカウントあたりの最大同時実行可能ジョブ数の制限を超過しているときに、ジョブはキューに挿入されます。 優先度の設定により、キューに挿入されたジョブのシーケンスが決まります。数値が小さいほど、優先度は高くなります。
    * Running (実行中):ジョブが実際に Data Lake Analytics アカウントで実行されています。
    * 終了処理中:ジョブが完了しています (たとえば、ファイルの終了処理を行っています)。
      
      ジョブは、すべてのフェーズで失敗する可能性があります。 たとえば、"準備中" フェーズではコンパイル エラー、"キューに挿入済み" フェーズではタイムアウト エラー、"実行中" フェーズでは実行エラーが発生する場合があります。
  * 基本情報
    
      [Job Summary (ジョブの概要)] パネルの下部には、ジョブの基本情報が表示されます。
    
      ![テキストボックス内の説明付きでジョブの概要を示すスクリーンショット。](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * ジョブ結果:[成功] または [失敗]。 ジョブは、すべてのフェーズで失敗する可能性があります。
    * 合計時間:送信時刻から終了時刻までの実時間 (期間)。
    * 合計計算時間:各頂点の実行時間の合計。ジョブが 1 つの頂点だけで実行された時間と考えることができます。 頂点の詳細は、[Total Vertices (頂点の総数)] で確認できます。
    * 送信時刻/開始時刻/終了時刻:Data Lake Analytics サービスがジョブの送信を受信した時刻、ジョブの実行を開始した時刻、およびジョブが正常に完了した (または完了しなかった) 時刻。
    * コンパイル/キューに挿入済み/実行中:"準備中"、"キューに挿入済み"、および "実行中" フェーズでかかった実時間。
    * アカウント:ジョブの実行に使用された Data Lake Analytics アカウント。
    * 作成者:ジョブを送信したユーザー。これは、実際のユーザーのアカウントまたはシステム アカウントの場合があります。
    * 優先順位:ジョブの優先度。 数値が小さいほど、優先度は高くなります。 これは、キュー内のジョブのシーケンスのみに影響します。 優先度を高くしても、実行中のジョブには作用しません。
    * 並行処理:要求された同時実行 Azure Data Lake Analytics ユニット (ADLAU) (頂点とも呼ばれます) の最大数。 現時点では、1 つの頂点は、2 つの仮想コアと 6 GB RAM を持つ 1 つの VM に相当しますが、これは、将来の Data Lake Analytics の更新プログラムでアップグレードされる可能性があります。
    * 残りのバイト数:ジョブが完了するまでに処理する必要があるバイト数。
    * 読み取りバイト数/書き込みバイト数:ジョブの実行が開始されてから読み取られたバイト数と書き込まれたバイト数。
    * 合計頂点:ジョブは複数の処理に分割されます。各作業は頂点と呼ばれます。 この値は、ジョブを構成する処理の数を表します。 頂点は、基本的なプロセス単位 (Azure Data Lake Analytics ユニット (ADLAU) とも呼ばれます) と見なすことができます。さらに、頂点は、並行処理で実行できます。 
    * 完了/実行中/失敗:完了した頂点、実行中の頂点、および失敗した頂点の数。 頂点はユーザー コード エラーとシステム障害の両方が原因で失敗する可能性がありますが、システムは、失敗した頂点を自動的に複数回再試行します。 再試行しても頂点が失敗する場合は、ジョブ全体が失敗します。
* [ジョブ グラフ]
  
    U-SQL スクリプトは、入力データを出力データに変換するロジックを表します。 スクリプトは、"準備中" フェーズでコンパイルされ、物理的な実行プランに最適化されます。 [ジョブ グラフ] には、物理的な実行プランが表示されます。  次の図にこのプロセスを示します。
  
    ![Azure Data Lake Analytics ジョブのフェーズを示す状態](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    ジョブは複数の処理に分割されます。 各処理は頂点と呼ばれます。 頂点はスーパー頂点 (ステージとも呼ばれます) としてグループ化され、ジョブ グラフとして視覚化されます。 ジョブ グラフ内の緑色のステージ プラカードは、ステージを表します。
  
    1 つのステージ内の各頂点は、同じデータの異なる部分に対して同じ処理を行っています。 たとえば、1 TB のデータ ファイルがあり、そのファイルから読み取る頂点が多数ある場合、各頂点はチャンクを読み取っています。 これらの頂点は、同じステージにグループ化され、同じ入力ファイルの異なる部分に対して同じ処理を行っています。
  
  * <a name="state-information"></a>ステージ情報
    
      特定のステージでは、いくつかの数字がプラカードに表示されます。
    
      ![Azure Data Lake Analytics のジョブ グラフのステージ](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract:ステージの名前。番号と操作方法を使用して名前が付けられます。
    * 84 vertices (頂点数: 84):このステージに含まれる頂点の総数。 この数字は、このステージで分割されている処理の数を示します。
    * 12.90 s/vertex (12.90 秒/頂点):このステージの頂点の平均実行時間。 この数字は、"SUM (すべての頂点の実行時間) / (頂点の総数)" で計算されます。 つまり、平行処理で実行されるすべての頂点を割り当てた場合は、ステージ全体が 12.90 秒で完了します。 さらに、これは、このステージのすべての処理を順次実行した場合、そのコストは "頂点数 * 平均時間" になることを意味します。
    * 850,895 rows written (書き込まれた行数: 850,895):このステージで書き込まれた行の総数。
    * R/W:このステージで読み取りまたは書き込みが行われたデータの量 (バイト単位)。
    * 色:ステージの頂点は、状態が異なるごとに色分けされて示されます。
      
      * 緑色は、頂点が成功したことを示します。
      * オレンジ色は、頂点が再試行されたことを示します。 再試行された頂点は失敗しても、システムによって自動的に再試行されて成功し、ステージ全体が正常に完了します。 頂点が再試行されてもまだ失敗する場合は、色が赤に変わり、ジョブ全体が失敗します。
      * 赤色は失敗を示します。つまり、特定の頂点がシステムによって複数回再試行されたにもかかわらず失敗したことを示します。 このシナリオでは、ジョブ全体が失敗します。
      * 青色は、特定の頂点が実行中であることを意味します。
      * 白色は、頂点が待機状態にあることを示します。 この場合、頂点は、ADLAU が使用可能になった後でスケジュールされるのを待っているか、入力データの準備が整っていないために入力を待っています。
      
      ステージの詳細は、マウス カーソルをいずれかの状態に合わせると確認できます。
      
      ![Azure Data Lake Analytics のジョブ グラフのステージの詳細](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * 頂点:頂点の詳細 (頂点の総数、完了した頂点の数、失敗した頂点の数、実行中または待機中の頂点の数など) を示します。
  * ポッド間のデータ読み取り/ポッド内のデータ読み取り:ファイルおよびデータは、分散ファイル システムの複数のポッドに格納されます。 ここの値は、同じポッド内またはポッド間で読み取られたデータの量を示します。
  * 合計計算時間:ステージの各頂点の実行時間の合計。これは、ステージのすべての処理が 1 つの頂点だけで実行された場合に要する時間と考えることができます。
  * Data and rows written/read (読み取り/書き込みデータおよび行):読み取り/書き込みが行われたデータ量や行数、または読み取りが必要なデータ量や行数を示します。
  * 頂点の読み取りの失敗:データの読み取りに失敗した頂点の数を示します。
  * 頂点の重複の破棄:頂点の実行があまりに遅い場合、システムは、複数の頂点が同じ処理を実行するようにスケジュールすることがあります。 いずれかの頂点が正常に完了すると、重複する頂点は破棄されます。 [Vertex duplicate discards (重複する頂点の破棄数)] には、ステージで重複として破棄された頂点の数が記録されます。
  * 頂点の失効:頂点は成功しましたが、何かの理由で後から再実行されます。 たとえば、下流の頂点は中間入力データを失った場合に、上流の頂点に再実行を求めます。
  * 頂点のスケジュールの実行:頂点がスケジュールされている時間の合計。
  * 読み取られた最小頂点データ/読み取られた平均頂点データ/読み取られた最大頂点データ:各頂点の読み取りデータの最小値、平均値、最大値。
  * 期間:ステージに要した実時間。この値を表示するには、プロファイルを読み込む必要があります。
  * ジョブの再生
    
      Data Lake Analytics は、ジョブを実行し、ジョブの頂点の実行情報 (頂点の開始時刻、停止時刻、失敗した時刻、再試行方法など) をアーカイブします。すべての情報は自動的にクエリ ストアに記録され、ジョブ プロファイルに格納されます。 ジョブ プロファイルは、ジョブ ビューの [Load Profile (プロファイルの読み込み)] を使用してダウンロードできます。ジョブ プロファイルをダウンロードした後、ジョブの再生を表示できます。
    
      ジョブの再生とは、クラスターで行われた処理の概要を視覚的に表したものです。 これにより、ジョブ実行の進行状況を監視し、視覚的にパフォーマンス上の問題やボトルネックを短時間 (通常は 30 秒未満) で検出できます。
  * ジョブ ヒート マップの表示 
    
      ジョブ ヒート マップは、ジョブ グラフの [Display (表示)] ボックスの一覧から選択できます。 
    
      ![Azure Data Lake Analytics のジョブ グラフのヒート マップの表示](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      ここには、ジョブの I/O、時間、スループットなどのヒート マップが示されます。これを使用すると、ジョブで最も時間がかかった部分や、ジョブが I/O 境界ジョブであるかどうかなどを確認できます。
    
      ![Azure Data Lake Analytics のジョブ グラフのヒート マップの例](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * 進行状況:ジョブ実行の進行状況。「ステージ情報」の説明を参照してください。
    * 読み取られたデータ/書き込まれたデータ:各ステージで読み取られた/書き込まれたデータの合計のヒート マップ。
    * 時間を計算します:SUM (すべての頂点の実行時間) のヒート マップ。これは、ステージのすべての処理が 1 つの頂点だけで実行された場合に要する時間と考えることができます。
    * ノードあたりの平均実行時間:"SUM (すべての頂点の実行時間) / (頂点の数)" のヒート マップ。 つまり、並行処理で実行されるすべての頂点を割り当てた場合は、ステージ全体がこの時間枠内で完了します。
    * 入力スループット/出力スループット:各ステージの入力/出力スループットのヒート マップ。これにより、ジョブが I/O バインド ジョブかどうかを確認できます。
* [Metadata Operations (メタデータ操作)]
  
    データベースの作成、テーブルの削除など、いくつかのメタデータ操作を U-SQL スクリプト内で実行できます。これらの操作は、コンパイル後に [Metadata Operations (メタデータ操作)] に表示されます。 ここでは、アサーション、エンティティの作成、エンティティの削除が表示されています。
  
    ![Azure Data Lake Analytics のジョブ ビューのメタデータ操作](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* [State History (状態の履歴)]
  
    状態の履歴は [Job Summary (ジョブの概要)] でも視覚化されますが、ここでは詳細を確認できます。 詳細な情報には、ジョブが準備された時刻、キューに挿入された時刻、実行が開始された時刻、実行が終了した時刻などがあります。 さらに、ジョブがコンパイルされた回数 (CcsAttempts: 1)、ジョブが実際にクラスターにディスパッチされた日時 (Detail: Dispatching job to cluster) などの情報も表示されます。
  
    ![Azure Data Lake Analytics のジョブ ビューの状態の履歴](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* 診断
  
    このツールは、ジョブの実行を自動的に診断します。 ジョブにエラーやパフォーマンスの問題がある場合は、アラートが表示されます。 ここで詳しい情報を表示するにはプロファイルのダウンロードが必要であることに注意してください。 
  
    ![Azure Data Lake Analytics のジョブ ビューの診断](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * 警告:コンパイラ警告と共にアラートがここに表示されます。 アラートが表示されたら、"x 件の問題" リンクをクリックするとより詳しい情報を表示できます。
  * Vertex run too long (頂点の実行時間が長すぎる):任意の頂点で時間 (たとえば 5 時間) がなくなった場合は、ここで問題を確認できます。
  * リソース使用量:割り当てられている量が並行処理に必要な量を超える場合や必要な量に満たない場合、問題がここに表示されます。 また、[Resource usage (リソース使用量)] をクリックすると、より詳細な情報が表示され、what-if シナリオを実行してより適切なリソース割り当て量を確認できます (詳細については、このガイドを参照してください)。
  * メモリの確認:任意の頂点で 5 GB を超えるメモリが使用されている場合、問題がここに表示されます。 使用されるメモリがシステムの制限を越えた場合は、ジョブの実行がシステムによって強制終了される可能性があります。

## <a name="job-detail"></a>Job Detail (ジョブの詳細)
[Job Detail (ジョブの詳細)] には、[スクリプト]、[リソース]、[Vertex Execution View (頂点実行ビュー)] を含む、ジョブの詳細情報が表示されます。

![Azure Data Lake Analytics のジョブの詳細](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* スクリプト
  
    ジョブの U-SQL スクリプトはクエリ ストアに格納されます。 元の U-SQL スクリプトを表示し、必要に応じて再送信できます。
* リソース
  
    [リソース] では、クエリ ストアに格納されているジョブのコンパイル出力が表示されます。 たとえば、ここでは、ジョブ グラフ、登録したアセンブリなどの表示に使用する "algebra.xml" が見つかります。
* Vertex execution view (頂点実行ビュー)
  
    頂点実行の詳細が示されます。 ジョブ プロファイルは、読み取られたデータおよび書き込まれたデータの合計、実行時間、状態など、各頂点実行ログをアーカイブします。このビューを使用すると、ジョブの実行の詳細を確認できます。 詳細については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* 診断情報のログについては、「 [Accessing diagnostics logs for Azure Data Lake Analytics (Azure Data Lake Analytics の診断ログへのアクセス)](data-lake-analytics-diagnostic-logs.md)
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* 頂点実行ビューの使用方法については、「[Use the Vertex Execution View in Data Lake Tools for Visual Studio (Data Lake Tools for Visual Studio での頂点実行ビューの使用)](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。

