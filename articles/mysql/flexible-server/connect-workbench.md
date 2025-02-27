---
title: クイック スタート:接続する - MySQL Workbench - Azure Database for MySQL - フレキシブル サーバー
description: このクイックスタートでは、MySQL Workbench を使用して、Azure Database for MySQL - フレキシブル サーバーに接続してデータのクエリを実行するための手順を紹介します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90946231"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>クイック スタート:MySQL Workbench を使用して Azure Database for MySQL - フレキシブル サーバー (プレビュー) に接続し、データのクエリを実行する


> [!IMPORTANT] 
> Azure Database for MySQL - フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

このクイックスタートでは、MySQL Workbench アプリケーションを使用して Azure Database for MySQL フレキシブル サーバーに接続する方法を紹介します。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。

- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](./quickstart-create-server-portal.md)
- [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーを作成する](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>クライアント ワークステーションを準備する
- "*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成して、フレキシブル サーバーと共に作成された VNet に追加できます。 「[Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理する](./how-to-manage-virtual-network-cli.md)」を参照してください。
- *パブリック アクセス (使用できる IP アドレス)* を指定してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。 「[Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-cli.md)」を参照してください。

- [MySQL Web サイト](https://dev.mysql.com/downloads/workbench/)から MySQL Workbench をダウンロードしてお使いのコンピューターにインストールします。

## <a name="get-connection-information"></a>接続情報の取得

フレキシブル サーバーに接続するために必要な接続情報を取得します。 完全修飾サーバー名とサインイン資格情報が必要です。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名を選択します。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL Workbench を使用したサーバーへの接続

MySQL Workbench を使用して Azure Database for MySQL フレキシブル サーバーに接続するには、次の手順に従います。

1. コンピューター上で MySQL Workbench アプリケーションを起動します。

2. **[Setup New Connection]\(新しい接続のセットアップ\)** ダイアログ ボックスで、次の情報を **[Parameters]\(パラメーター\)** タブに入力します。

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="新しい接続のセットアップ":::

    | **パラメーター** | **推奨値** | **フィールドの説明** |
    |---|---|---|
    |    接続名 | Demo Connection | この接続のラベルを指定します。 |
    | 接続方法 | Standard (TCP/IP) | Standard (TCP/IP) で十分です。 |
    | hostname | *サーバー名* | 前に Azure Database for MySQL を作成したときに使用したサーバー名の値を指定します。 この例におけるサーバーは、mydemoserver.mysql.database.azure.com です。 例で示されているように、完全修飾ドメイン名 (\*.mysql.database.azure.com) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。  |
    | Port | 3306 | Azure Database for MySQL に接続する場合は、常にポート 3306 を使用します。 |
    | ユーザー名 |  *サーバー管理者ログイン名* | 前に Azure Database for MySQL を作成したときに指定したサーバー管理者ログイン ユーザー名を入力します。 この例のユーザー名は myadmin です。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。
    | Password | パスワード | **[Store in Vault]\(コンテナーに保存\)** ボタンをクリックしてパスワードを保存します。 |

3. **[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。

4. **[OK]** をクリックして接続を保存します。

5. **[MySQL Connections]\(MySQL 接続\)** の一覧で、対象のサーバーに対応するタイルをクリックし、接続が確立するのを待ちます。

    新しい SQL タブが開き、クエリを入力できる空白のエディターが表示されます。

> [!NOTE]
> TLS 1.2 を使用して暗号化された接続は必須であり、Azure Database for MySQL フレキシブル サーバーに適用されます。 通常、MySQL Workbench がサーバーに接続するために、TLS/SSL 証明書を使用した追加構成は必要ありませんが、TLS/SSL CA 証明書と MySQL Workbench のバインドをお勧めします。 詳細については、「[TLS/SSL を使用して接続する](./how-to-connect-tls-ssl.md)」を参照してください。

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>テーブルの作成、データの挿入/読み取り/更新/削除

1. いくつかのサンプル データを示すために、サンプル SQL コードをコピーして空の SQL タブに貼り付けます。

    このコードでは、quickstartdb という名前の空のデータベースを作成し、inventory という名前のサンプル テーブルを作成します。 いくつかの行を挿入した後、行を読み取ります。 次に、更新ステートメントによってデータを変更し、行を再度読み取ります。 最後に、1 つの行を削除した後、行を再度読み取ります。

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    このスクリーンショットは、SQL Workbench に表示された SQL コードとその実行後の出力の例を示しています。

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="サンプル SQL コードを実行するための MySQL Workbench の SQL タブ":::

2. サンプル SQL コードを実行するには、 **[SQL File]\(SQL ファイル\)** タブのツールバーにある稲妻アイコンをクリックします。
3. ページ中央の **[結果グリッド]** セクションの 3 つのタブに結果が表示されていることに注目してください。
4. ページの下部にある **[Output]\(出力\)** リストに注目してください。 各コマンドの状態が表示されています。

これで、MySQL Workbench を使用して Azure Database for MySQL フレキシブル サーバーに接続し、SQL 言語を使用してデータのクエリを実行できました。

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL - フレキシブル サーバーでのトランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続](./how-to-connect-tls-ssl.md)
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-portal.md)
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理する](./how-to-manage-virtual-network-portal.md)
