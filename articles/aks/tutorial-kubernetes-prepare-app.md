---
title: "Azure 上的 Kubernetes 教學課程 - 準備應用程式"
description: "AKS 教學課程 - 準備應用程式"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0c4a1459a49fb60578f9f38ea65cd1400b538382
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="prepare-application-for-azure-container-service-aks"></a>準備 Azure Container Service (AKS) 的應用程式

在本教學課程 (八個章節的第一部分) 中，多容器應用程式已準備好用於 Kubernetes。 完成的步驟包括：  

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 從應用程式來源建立容器映像
> * 在本機 Docker 環境中測試應用程式

完成後，就可在本機開發環境中存取下列應用程式。

![Azure 上 Kubernetes 叢集的影像](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

在後續的教學課程中，此容器映像會上傳至 Azure Container Registry，然後在 AKS 叢集中執行。

## <a name="before-you-begin"></a>開始之前

本教學課程假設使用者對核心 Docker 概念有基本認識，例如容器、容器映像和基本 Docker 命令。 如有需要，請參閱[開始使用 Docker][docker-get-started] 以取得容器基本概念入門。 

若要完成本教學課程，您需要 Docker 開發環境。 Docker 提供可輕鬆在 [Mac][docker-for-mac]、[Windows][docker-for-windows] 或 [Linux][docker-for-linux] 系統上設定 Docker 的套件。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 因此，我們建議使用完整的 Docker 開發環境。

## <a name="get-application-code"></a>取得應用程式程式碼

本教學課程中使用的範例應用程式是基本投票應用程式。 應用程式是由前端 Web 元件和後端 Redis 執行個體所組成。 Web 元件會封裝至自訂容器映像。 Redis 執行個體會從 Docker Hub 使用未修改的映像。  

使用 git 將應用程式的複本下載至您的開發環境。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

變更目錄以從複製的目錄操作。

```console
cd azure-voting-app-redis
```

目錄內有應用程式原始程式碼、預先建立的 Docker Compose 檔案和 Kubernetes 資訊清單檔。 整套教學課程都會使用到這些檔案。 

## <a name="create-container-images"></a>建立容器映像

[Docker Compose][docker-compose] 可用來將容器映像的組建和多容器應用程式的部署進行自動化。

執行 `docker-compose.yaml`檔案以建立容器映像、下載 Redis 映像，並啟動應用程式。

```console
docker-compose up -d
```

完成時，使用 [docker images][docker-images] 命令來查看所建立的映像。

```console
docker images
```

請注意，已下載或建立三個映像。 `azure-vote-front` 映像含有應用程式，並使用 `nginx-flask` 映像做為基礎映像。 `redis` 映像可用來啟動 Redis 執行個體。

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

執行 [docker ps][docker-ps] 命令可查看執行中的容器。

```console
docker ps
```

輸出：

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>在本機測試應用程式

瀏覽至 http://localhost:8080 以查看執行中的應用程式。

![Azure 上 Kubernetes 叢集的影像](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>清除資源

現已驗證應用程式功能，可以停止並移除執行中的容器。 請勿刪除容器映像。 `azure-vote-front`映像會在下一個教學課程中上傳至 Azure Container Registry 執行個體。

執行下列命令來停止執行中的容器。

```console
docker-compose stop
```

使用下列命令來刪除已停止的容器和資源。

```console
docker-compose down
```

完成時，您有包含 Azure Vote 應用程式的一個容器映像。

## <a name="next-steps"></a>後續步驟

在本教學課程中，應用程式已經過測試並已建立應用程式的容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 從 GitHub 複製應用程式來源  
> * 已從應用程式來源建立容器映像
> * 已在本機 Docker 環境中測試應用程式

前往下一個教學課程，了解如何在 Azure Container Registry 中儲存容器映像。

> [!div class="nextstepaction"]
> [將映像推送至 Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md