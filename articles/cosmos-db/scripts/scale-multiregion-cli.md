---
title: "Azure CLI 指令碼 - Azure Cosmos DB 的多區域複寫 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - Azure Cosmos DB 的多區域複寫"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cde5ff007dc7b26db33e195dd6cb528675ecd391
ms.contentlocale: zh-tw
ms.lasthandoff: 05/15/2017

---

# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-the-azure-cli"></a>使用 Azure CLI 複寫多個區域中的 Azure Cosmos DB 資料庫帳戶和設定容錯移轉優先順序

此範例會使用 Azure CLI 複寫多個區域中的任何一種 Azure Cosmos DB 資料庫帳戶，並設定容錯移轉優先順序。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/cosmosdb/scale-cosmosdb-replicate-multiple-regions/scale-cosmosdb-replicate-multiple-regions.sh?highlight=21-31 "將 Azure Cosmos DB 擴展至多個區域")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | 更新 Azure Cosmos DB 帳戶。 |
| [az group delete](/cli/azure/resource#delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Cosmos DB CLI 文件](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 指令碼範例。

