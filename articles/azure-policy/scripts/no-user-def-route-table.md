---
title: Azure 原則 JSON 範例 - 禁止使用者定義的路由表 | Microsoft Docs
description: 此 JSON 範例原則會禁止使用使用者定義的路由表來部署虛擬網路。
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: b4481f2315aef91c5725b32d75995422435e6e04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603119"
---
# <a name="no-user-defined-route-table"></a>禁止使用者定義的路由表

此原則會禁止使用使用者定義的路由表來部署虛擬網路。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>範例範本

[!code-json[main](../../../policy-templates/samples/Network/no-route-table-in-ER-Network/azurepolicy.json "No User Defined Route Table")]

您可以使用 [Azure 入口網站](#deploy-with-the-portal) [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 來部署此範本。

## <a name="deploy-with-the-portal"></a>使用入口網站部署

[![部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fno-route-table-in-ER-Network%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "no-route-table-in-ER-Network" -DisplayName "No User Defined Route Table" -description "Forbid virtual networks to use user defined route table" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-route-table-in-ER-Network/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-route-table-in-ER-Network/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清除 PowerShell 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'no-route-table-in-ER-Network' --display-name 'No User Defined Route Table' --description 'Forbid virtual networks to use user defined route table' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-route-table-in-ER-Network/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-route-table-in-ER-Network/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "no-route-table-in-ER-Network"
```

### <a name="clean-up-azure-cli-deployment"></a>清除 Azure CLI 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../json-samples.md)檢閱更多範例。