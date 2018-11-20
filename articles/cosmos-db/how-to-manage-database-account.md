---
title: 了解如何在 Azure Cosmos DB 中管理資料庫帳戶
description: 了解如何在 Azure Cosmos DB 中管理資料庫帳戶
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 0683516d16bf1501eee83901c5171811b8c0e44d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621542"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中管理資料庫帳戶

本文說明如何管理 Azure Cosmos DB 帳戶，以設定內送流量備援容錯機制、新增/移除區域、設定多個寫入區域，和設定容錯移轉優先順序。 

## <a name="create-a-database-account"></a>建立資料庫帳戶

### <a id="create-database-account-via-portal"></a>Azure 入口網站

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>設定內送流量備援容錯機制的用戶端

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>在資料庫帳戶中新增/移除區域

### <a id="add-remove-regions-via-portal"></a>Azure 入口網站

1. 瀏覽至 Azure Cosmos DB 帳戶並開啟 [全域複寫資料] 功能表。

2. 若要新增區域，請按一下有 **"+"** 標籤對應於所需區域的空白六邊形，以選取地圖中的一或多個區域。 您也可以選取 [+ 新增區域] 選項，然後從下拉式功能表中選擇區域，藉以新增區域。

3. 若要移除區域，請按一下具有勾號的藍色六邊形以取消選取地圖中的一或多個區域，或選取右手邊區域旁邊的「垃圾桶」(🗑) 圖示。

4. 按一下 [儲存] 以儲存變更。

   ![新增/移除區域功能表](./media/how-to-manage-database-account/add-region.png)

在單一區域寫入模式中，您無法移除寫入區域。 您必須先容錯移轉至不同的區域，才能刪除目前的寫入區域。

在多重區域寫入模式中，只要您有至少一個區域，即可新增/移除任何區域。

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>設定多重寫入區域

### <a id="configure-multiple-write-regions-portal"></a>Azure 入口網站

當您建立資料庫帳戶時，請確定已啟用 [多重區域寫入] 設定。

![Azure Cosmos 帳戶建立螢幕擷取畫面](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager 範本

下列 JSON 程式碼是範例 Resource Manager 範本。 您可以使用它來部署一致性原則為「限定過期」、最大過期間隔為 5 秒，以及容許的過時要求數目上限為 100 的 Azure Cosmos 帳戶。 若要了解 Resource Manager 範本格式和語法，請參閱 [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 文件。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>啟用 Azure Cosmos 帳戶的手動容錯移轉

### <a id="enable-manual-failover-via-portal"></a>Azure 入口網站

1. 瀏覽至 Azure Cosmos 帳戶並開啟 [全域複寫資料] 功能表。

2. 按一下功能表頂端的 [手動容錯移轉] 按鈕。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [手動容錯移轉] 功能表上選取新的寫入區域，並選取方塊表示您了解此選項將變更您的寫入區域。

4. 按一下 [確定] 來觸發容錯移轉。

   ![手動容錯移轉入口網站功能表](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>啟用 Azure Cosmos 帳戶的自動容錯移轉

### <a id="enable-automatic-failover-via-portal"></a>Azure 入口網站

1. 從 Azure Cosmos 帳戶，開啟 [全域複寫資料] 窗格。 

2. 按一下窗格頂端的 [自動容錯移轉] 按鈕。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [自動容錯移轉] 窗格中，確定 [啟用自動容錯移轉] 設定為 [開啟]。 

4. 按一下功能表底部的 [儲存]。

   ![自動容錯移轉入口網站功能表](./media/how-to-manage-database-account/automatic-failover.png)

您也可以在此功能表上設定容錯移轉優先順序。

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>設定 Azure Cosmos 帳戶的容錯移轉屬性

### <a id="set-failover-priorities-via-portal"></a>Azure 入口網站

1. 從 Azure Cosmos 帳戶，開啟 [全域複寫資料] 窗格。 

2. 按一下窗格頂端的 [自動容錯移轉] 按鈕。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [自動容錯移轉] 窗格中，確定 [啟用自動容錯移轉] 設定為 [開啟]。 

4. 按一下讀取區域並透過當您暫留其上時出現在資料列左側的三個點進行拖曳，即可修改容錯移轉優先順序。 

5. 按一下功能表底部的 [儲存]。

   ![自動容錯移轉入口網站功能表](./media/how-to-manage-database-account/automatic-failover.png)

您無法在此功能表上修改寫入區域。 若要手動變更寫入區域，您必須執行手動容錯移轉。

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>後續步驟

您可以透過下列文件，了解如何管理 Azure Cosmos DB 的一致性層級和資料衝突：

* [如何管理一致性](how-to-manage-consistency.md)
* [如何管理區域之間的衝突](how-to-manage-conflicts.md)

