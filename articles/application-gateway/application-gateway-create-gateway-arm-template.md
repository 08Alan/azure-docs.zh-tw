---
title: "建立 Azure 應用程式閘道 - 範本 | Microsoft Docs"
description: "本頁面提供使用 Azure 資源管理員範本，建立 Azure 應用程式閘道的指示。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8192ee25-d9f0-4b32-a45e-1d74629c54e5
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f550ec9a8d254378d165f0c842459fd50ade7945
ms.openlocfilehash: 5cdd26529ed06b7903ae232ab8da4527e79a8a68
ms.lasthandoff: 03/30/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>使用 Azure 資源管理員範本建立應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 應用程式閘道是第&7; 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道提供許多應用程式傳遞控制器 (ADC) 功能，包括 HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、安全通訊端層 (SSL) 卸載、自訂健全狀態探查、多網站支援，以及許多其他功能。 若要尋找完整的支援功能清單，請瀏覽 [應用程式閘道概觀](application-gateway-introduction.md)

您會了解如何從 GitHub 下載和修改現有 Azure Resource Manager 範本，以及從 GitHub、PowerShell 和 Azure CLI 部署範本。

如果您只需直接從 GitHub 部署 Azure 資源管理員範本而不做任何變更，請跳至＜從 GitHub 部署範本＞。

## <a name="scenario"></a>案例

在此案例中，您將會：

* 建立含有兩個執行個體的應用程式閘道。
* 建立名為 VirtualNetwork1 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路。
* 針對想要用來為流量進行負載平衡的 Web 伺服器，設定兩個先前所設定的後端 IP。 在此範本範例中，後端 IP 是 10.0.1.10 和 10.0.1.11。

> [!NOTE]
> 這些設定都是適用於此範本的參數。 若要自訂範本，您可以變更規則、接聽程式以及會開啟 azuredeploy.json 的 SSL。

![案例](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>下載並了解 Azure 資源管理員範本

您可以下載現有 Azure 資源管理員範本，以透過 Github 建立虛擬網路和兩個子網路，然後進行任何需要的變更，並重複使用該範本。 若要這樣做，請使用下列步驟：

1. 瀏覽至 [建立應用程式閘道](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create)。
2. 依序按一下 [azuredeploy.json] 和 [RAW]。
3. 將檔案儲存至您電腦上的本機資料夾。
4. 如果您熟悉 Azure 資源管理員範本的使用方式，請跳至步驟 7。
5. 開啟您儲存的檔案，並查看第 5 行中 **參數** 下方的內容。 Azure 資源管理員範本的參數提供值的預留位置，可以在部署期間填寫。
   
   | 參數 | 說明 |
   | --- | --- |
   | **位置** |建立應用程式閘道的 Azure 區域 |
   | **VirtualNetwork1** |新的虛擬網路名稱 |
   | **addressPrefix** |以 CIDR 格式表示的虛擬網路位址空間 |
   | **ApplicationGatewaysubnet** |應用程式閘道子網路的名稱 |
   | **subnetPrefix** |應用程式閘道子網路的 CIDR 區塊 |
   | **skuname** |SKU 執行個體大小 |
   | **容量** |執行個體數目 |
   | **backendaddress1** |第一部 Web 伺服器的 IP 位址 |
   | **backendaddress2** |第二部 Web 伺服器的 IP 位址 |

    > [!IMPORTANT]
    >GitHub 所維護的 Azure 資源管理員範本可能會隨著時間改變。 使用範本前，請務必先檢查當中的內容。

6. 檢查 **resources** 下方的內容，並注意下列屬性：

   * **type**。 範本所建立的資源類型。 在此案例中，類型是 `Microsoft.Network/applicationGateways`，代表應用程式閘道。
   * **名稱**。 資源的名稱。 請注意 `[parameters('applicationGatewayName')]` 的用法，這表示此名稱是在部署期間由您輸入的內容，或是由參數檔案所提供。
   * **屬性**。 資源屬性的清單。 此範本會在應用程式閘道建立期間，使用虛擬網路與公用 IP 位址。

7. 瀏覽回 [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create)。
8. 依序按一下 [azuredeploy-paremeters.json] 和 [RAW]。
9. 將檔案儲存至您電腦上的本機資料夾。
10. 開啟您儲存的檔案，以編輯參數的值。 使用下列值來部署本文案例所述的應用程式閘道。

    ```json
        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }
    ```

11. 儲存檔案。 您可以使用線上 JSON 驗證工具 (例如 [JSlint.com](http://www.jslint.com/))，來測試 JSON 範本和參數範本。

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>使用 PowerShell 來部署 Azure 資源管理員範本

如果您從未用過 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) ，並遵循指示登入 Azure，然後選取您的訂用帳戶。

### <a name="step-1"></a>步驟 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>步驟 2

檢查帳戶的訂用帳戶。

```powershell
Get-AzureRmSubscription
```

系統會提示使用您的認證進行驗證。

### <a name="step-3"></a>步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步驟 4

如有需要，請使用 **New-AzureResourceGroup** Cmdlet 建立資源群組。 在下列範例中，您會在美國東部位置建立名為 AppgatewayRG 的資源群組。

```powershell
New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
```

執行 **New-AzureRmResourceGroupDeployment** Cmdlet，使用先前下載並修改的範本和參數檔案來部署新的虛擬網路。

```powershell
New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>使用 Azure CLI 來部署 Azure 資源管理員範本

若要使用 Azure CLI 部署您下載的 Azure Resource Manager 範本，請依照下列步驟執行：

### <a name="step-1"></a>步驟 1

如果您從未使用過 Azure CLI，請參閱 [安裝和設定 Azure CLI](../xplat-cli-install.md) ，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶。

### <a name="step-2"></a>步驟 2

執行 **azure config mode** 命令，以切換為 Resource Manager 模式，如下列程式碼片段中所示。

```azurecli
azure config mode arm
```

此為上述命令的預期輸出內容：

```azurecli
info:    New mode is arm
```

### <a name="step-3"></a>步驟 3

如有必要，請執行 **azure group create** 命令來建立新的資源群組，如下列程式碼片段中所示。 請查看命令的輸出內容。 輸出後顯示的清單可說明所使用的參數。 如需資源群組的詳細資訊，請瀏覽 [Azure Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

```azurecli
azure group create -n appgatewayRG -l eastus
```

**-n (or --name)**。 新資源群組的名稱。 在本文案例中為「appgatewayRG」 。

**-l (或 --location)**。 建立新資源群組的 Azure 區域。 在本文案例中為「eastus」 。

### <a name="step-4"></a>步驟 4

執行 **azure group deployment create** Cmdlet，使用在上一個步驟中下載並修改的範本和參數檔案來部署新的虛擬網路。 輸出後顯示的清單可說明所使用的參數。

```azurecli
azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>使用「按一下即部署」來部署 Azure 資源管理員範本

「按一下即部署」是另一種使用 Azure 資源管理員範本的方式。 這是將範本與 Azure 入口網站搭配使用的簡便方法。

### <a name="step-1"></a>步驟 1

移至 [建立含有公用 IP 的應用程式閘道](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/)。

### <a name="step-2"></a>步驟 2

按一下 [ **部署至 Azure**]。

![部署至 Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>步驟 3

在入口網站上填寫適用於部署範本的參數，然後按一下 [確定] 。

![參數](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>步驟 4

選取 [法律條款]，然後按一下 [購買]。

### <a name="step-5"></a>步驟 5

在 [自訂部署] 刀鋒視窗上，按一下 [建立] 。

## <a name="providing-certificate-data-to-resource-manager-templates"></a>提供 Resource Manager 範本的憑證資料

使用 SSL 搭配範本時，必須以 base64 字串形式提供憑證，而不是上傳憑證。 若要將 .pfx 或 .cer 轉換成 base64 字串，請執行下列 PowerShell 命令。 這個程式碼片段會將憑證轉換為可供範本使用的 base64 字串。 預期的輸出是可以儲存在變數並貼在範本中的字串。

```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="next-steps"></a>後續步驟

如果您想要設定 SSL 卸載，請參閱 [設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與內部負載平衡器搭配使用，請參閱 [建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請造訪：

* [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)


