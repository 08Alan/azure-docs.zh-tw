---
title: Azure PowerShell 指令碼：建立 Azure Cosmos DB 容錯移轉原則
description: Azure PowerShell 指令碼範例：建立 Azure Cosmos DB 容錯移轉原則
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.custom: mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 05/10/2017
ms.openlocfilehash: 831faf7122a2fff8297cdc390584add29ee13937
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962489"
---
# <a name="create-an-azure-cosmos-db-failover-policy-for-high-availability-using-powershell"></a>使用 PowerShell 建立高可用性的 Azure Cosmos DB 容錯移轉原則

此範例 PowerShell 指令碼會為 Azure Cosmos DB 建立高可用性的容錯移轉原則。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/cosmosdb/modify-failover-priority/modify-failover-priority.ps1?highlight=36-39,42-47 "Create an Azure Cosmos DB SQL API account")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource?view=azurermps-3.8.0) | 建立主機資料庫或彈性集區的邏輯伺服器。 |
| [Invoke-AzureRmResourceAction](https://docs.microsoft.com/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-3.8.0) | 在 Azure CosmosDB 帳戶上叫用動作。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Cosmos DB PowerShell 指令碼](../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 指令碼範例。