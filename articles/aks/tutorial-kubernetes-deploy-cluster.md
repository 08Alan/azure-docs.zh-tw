---
title: "Azure 上的 Kubernetes 教學課程 - 部署叢集"
description: "AKS 教學課程 - 部署叢集"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 975069dbe9283c98482d7d0d5741a595ef323b35
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>部署 Azure Container Service (AKS) 叢集

Kubernetes 會提供容器化應用程式的分散式平台。 透過 AKS，可簡單又快速地佈建生產環境就緒 Kubernetes 叢集。 在本教學課程 (3/8 部分) 中，將 Kubernetes 叢集部署在 AKS 中。 完成的步驟包括：

> [!div class="checklist"]
> * 部署 Kubernetes AKS 叢集
> * 安裝 Kubernetes CLI (kubectl)
> * 設定 kubectl

在後續的教學課程中，會相應放大、更新 Azure Vote 應用程式，且會將 Operations Management Suite 設定為監視 Kubernetes 叢集。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已建立容器映像並上傳到 Azure Container Registry 執行個體。 如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]。

## <a name="enable-aks-preview"></a>啟用 AKS 預覽

雖然 AKS 處於預覽狀態，但是建立新叢集需要訂用帳戶的功能旗標。 您可以為您想要使用之任意數量的訂用帳戶要求這項功能。 使用 `az provider register` 命令來註冊 AKS 提供者：

```azurecli
az provider register -n Microsoft.ContainerService
```

在註冊之後，您現在已準備就緒可以使用 AKS 建立 Kubernetes 叢集。

## <a name="create-kubernetes-cluster"></a>建立 Kubernetes 叢集

下列範例會在名為 `myResourceGroup` 的資源群組中，建立名為 `myAKSCluster` 的叢集。 我們已在[先前的教學課程][aks-tutorial-prepare-acr]中建立此資源群組。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

幾分鐘之後，部署就會完成，並以 JSON 格式傳回 AKS 部署的相關資訊。

## <a name="install-the-kubectl-cli"></a>安裝 kubectl CLI

若要從用戶端電腦連線到 Kubernetes 叢集，請使用 [kubectl][kubectl] (Kubernetes 命令列用戶端)。

如果您是使用 Azure CloudShell，則已安裝 kubectl。 如果您想要在本機進行安裝，請執行下列命令：

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>使用 kubectl 連線

若要設定 kubectl 來連線到 Kubernetes 叢集，請執行下列命令：

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

若要確認與叢集的連線，請執行 [kubectl get nodes][kubectl-get] 命令。

```azurecli
kubectl get nodes
```

輸出：

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

完成教學課程時，您的 AKS 叢集即已可供工作負載使用。 在後續的教學課程中，會將多容器應用程式部署到這個叢集、向外延展、更新，以及進行監視。

## <a name="configure-acr-authentication"></a>設定 ACR 驗證

您必須設定 AKS 叢集與 ACR 登錄之間的驗證。 這牽涉到授與 ACS 身分識別從 ACR 登錄提取映像的適當權限。

首先，取得針對 AKS 設定之服務主體的識別碼。 更新資源群組名稱和 AKS 叢集名稱，以符合您的環境。

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

取得 ACR 登錄資源識別碼。將登錄名稱更新為您的 ACR 登錄名稱，以及將資源群組更新為 ACR 登錄所在的資源群組。

```azurecli
ACR_ID=$(az acr show --name myACRRegistry --resource-group myResourceGroup --query "id" --output tsv)
```

建立角色指派，以授與適當的存取權。

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，Kubernetes 叢集已部署在 AKS 中。 已完成下列步驟：

> [!div class="checklist"]
> * 已部署 Kubernetes AKS 叢集
> * 已安裝 Kubernetes CLI (kubectl)
> * 已設定 kubectl

請前往下一個教學課程，以了解如何在叢集上執行應用程式。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署應用程式][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md