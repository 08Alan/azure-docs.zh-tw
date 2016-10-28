<properties
   pageTitle="連結資源的資源管理員範本 | Microsoft Azure"
   description="顯示可透過範本在相關資源之間部署連結的資源管理員結構描述。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# 資源連結範本結構描述

這會在兩個資源之間建立連結。該連結會套用至稱為「來源資源」的資源。連結中的第二個資源則稱為「目標資源」。

## 結構描述格式

若要建立連結，請將下列結構描述新增到範本的資源區段。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## 值

下表描述您在結構描述中必須設定的值。

| 名稱 | 值 |
| ---- | ---- |
| 類型 | 列舉<br />必要<br />**{命名空間}/{類型}/providers/links**<br /><br />要建立的資源類型。{namespace} 和 {type} 值指的是來源資源的提供者命名空間和資源類型。 |
| apiVersion | 列舉<br />必要<br />**2015-01-01**<br /><br />要用來建立資源的應用程式開發介面 (API) 版本。 |  
| 名稱 | 字串<br />必要<br />**{資源}/Microsoft.Resources/{連結名稱}**<br /> 最多 64 個字元，而且不能包含 <、>、%、&、? 或任何控制字元。<br /><br />同時指定來源資源名稱和連結名稱的值。| 
| dependsOn | 陣列<br />選用<br />以逗號分隔的資源名稱或資源唯一識別碼清單。<br /><br />此連結所相依的資源集合。如果您所連結的資源部署到相同的範本，請在此元素中包含那些資源的名稱，確保優先部署它們。| 
| 屬性 | 物件<br />必要<br />[物件](#properties)<br /><br />能識別要連結的資源，以及該連結之相關資訊的物件。| 

<a id="properties" />
### 屬性物件

| 名稱 | 值 |
| ------- | ---- |
| targetId | 字串<br />必要<br />**{資源識別碼}**<br /><br />要連結之目標資源的識別碼。| | 附註 | 字串<br />選用<br />最多 512 個字元<br /><br />鎖定的描述。|


## 如何使用連結資源

當兩個資源的相依性在部署後持續時，在它們之間套用連結。例如，某個 app 可能會連接到位於不同資源群組的資料庫。您可以藉由在該 app 和資料庫之間建立連結，來定義該相依性。連結能讓您記錄兩個資源之間的關聯性。稍後您或組織內的其他人可以向資源查詢連結，以探索該資源和其他資源一同運作的方式。

所有已連結的資源都必須屬於同一個訂用帳戶。每個資源都可以連結至其他 50 個資源。如果任何已連結的資源被刪除或移動，連結擁有者必須清除剩餘的連結。

若要透過 REST 使用連結，請參閱[連結的資源 (英文)](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

使用下列 Azure PowerShell 命令來查看訂用帳戶中的所有連結。您可以提供其他參數來限制結果。

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## 範例

下列範例會將唯讀鎖定套用到 Web 應用程式。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
        ],
        "outputs": {}
    }

## 快速入門範本

下列快速入門範本會利用連結部署資源。

- [利用邏輯應用程式警示加入佇列](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [利用邏輯應用程式警示放入 Slack](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [以現有閘道佈建 API 應用程式](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [以新的閘道佈建 API 應用程式](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [使用範本建立邏輯應用程式與 API 應用程式](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [會在引發警示時傳送文字訊息的邏輯應用程式](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## 後續步驟

- 如需範本結構的相關資訊，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

<!---HONumber=AcomDC_0406_2016-->