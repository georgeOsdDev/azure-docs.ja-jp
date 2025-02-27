---
title: クイックスタート - Azure でコンテナー イメージをオンデマンドでビルドする
description: Azure Container Registry コマンドを使用して、Azure クラウド内で Docker コンテナー イメージをオンデマンドでビルド、プッシュ、実行します。
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: c6fe1fc246d112218b492072155175b2db99c8c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032951"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>クイック スタート:Azure Container Registry タスクを使用したコンテナー イメージのビルドと実行

このクイックスタートでは、[Azure Container Registry タスク][container-registry-tasks-overview]のコマンドを使用して、Docker コンテナー イメージの迅速なビルド、プッシュ、実行を、Docker のローカル インストールなしで、Azure 内でネイティブに行います。 ACR タスクは、コンテナー ライフサイクル全体でコンテナー イメージを管理および変更するのに役立つ Azure Container Registry 内の機能のスイートです。 この例では、ローカルの Dockerfile を使用して、オンデマンドのビルドで "内部ループ" コンテナー イメージ開発サイクルをクラウドに任せる方法について説明します。 

このクイック スタートの後に、[チュートリアル](container-registry-tutorial-quick-task.md)を使用して ACR タスクのより高度な機能について説明します。 ACR タスクでは、コードのコミットに基づくイメージのビルドまたは基本イメージの更新を自動化したり、他のシナリオ間で複数のコンテナーを並列にテストしたりすることができます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- このクイックスタートには、Azure CLI のバージョン 2.0.58 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

コンテナー レジストリがまだない場合は、最初に [az group create][az-group-create] コマンドを使用してリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[az acr create][az-acr-create] コマンドを使用してコンテナー レジストリを作成します。 レジストリの名前は Azure 内で一意にする必要があります。また、5 ～ 50 文字の英数字を含める必要があります。 次の例では、*myContainerRegistry008* を使用しています。 これを一意の値に更新します。

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

この例では、Azure Container Registry について学習している開発者にとって、コストが最適なオプションである *Basic* レジストリを作成します。 利用可能なサービス レベルの詳細については、[コンテナー レジストリのサービス レベル][container-registry-skus]に関するページを参照してください。

## <a name="build-and-push-image-from-a-dockerfile"></a>Dockerfile からのイメージのビルドとプッシュ

ここでは、Azure Container Registry を使用してイメージをビルドし、プッシュします。 まず、ローカル作業ディレクトリを作成してから、`FROM mcr.microsoft.com/hello-world` の 1 行だけが記述された *Dockerfile* という名前の Dockerfile を作成します。 これは、Microsoft Container Registry でホストされている `hello-world` イメージから Linux コンテナー イメージをビルドする単純な例です。 独自の標準的な Dockerfile を作成して他のプラットフォーム用のイメージをビルドすることができます。 Bash シェルで作業している場合は、次のコマンドで Dockerfile を作成してください。

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

[az acr build][az-acr-build] コマンドを実行してイメージをビルドし、正常にイメージがビルドされたら、それをレジストリにプッシュします。 次の例では、`sample/hello-world:v1` イメージをビルドしてプッシュします。 コマンドの最後にある `.` では、Dockerfile の位置を設定します。この場合は現在のディレクトリです。

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

正常なビルドとプッシュからの出力は次のようになります。

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>イメージを実行する

ここでは、ビルドしてレジストリにプッシュしたイメージを迅速に実行します。 ここでは、[az acr run][az-acr-run] を使用してコンテナー コマンドを実行します。 コンテナー開発ワークフローでは、これはイメージをデプロイする前の検証手順になる可能性があります。または、[複数のステップの YAML ファイル][container-registry-tasks-multi-step]にコマンドを含めることもできます。 

次の例では、`$Registry` を使用して、コマンドの実行場所となるレジストリを指定します。

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

この例の `cmd` パラメーターでは既定の構成のコンテナーを実行しますが、`cmd` では追加の `docker run` パラメーターまたはその他の `docker` コマンドもサポートされます。

次のような出力になります。

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー レジストリ、そこに格納されているコンテナー イメージを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ACR タスクの機能を使用して、Docker コンテナー イメージの迅速なビルド、プッシュ、実行を、Docker のローカル インストールなしで、Azure 内でネイティブに行いました。 ACR タスクを使用してイメージのビルドと更新を自動化する方法を学習するには、Azure Container Registry タスクのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Registry タスクのチュートリアル][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
