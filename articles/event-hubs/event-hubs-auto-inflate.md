---
title: "自動相應增加 Azure 事件中樞輸送量單位 | Microsoft Docs"
description: "在命名空間上啟用自動擴充以自動相應增加輸送量單位"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 20ee0e6cff2a07cbd62a79799eada5708c7a0f07
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2018
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>自動相應增加 Azure 事件中樞輸送量單位

Azure 事件中樞為可高度擴充的資料串流平台。 因此，事件中樞使用量通常會在開始使用服務之後增加。 這類使用量需要增加預先決定的輸送量單位，以調整事件中樞並處理較大的傳輸速率。 「事件中樞」的「自動擴充」功能會自動相應增加輸送量單位數，來符合使用量需求。 增加輸送量單位可避免發生節流情況，其中：

* 資料輸入速率會超出所設定的輸送量單位。
* 資料輸出要求速率會超出所設定的輸送量單位。

## <a name="how-auto-inflate-works"></a>自動擴充的運作方式

事件中樞的流量會受到輸送量單位控制。 單一輸送量單位可允許每秒 1 MB 的輸入及此數量兩倍的輸出。 標準事件中樞可以設定為 1-20 個輸送量單位。 自動擴充可讓您於開始時使用最小的必要輸送量單位。 該功能接著會根據流量的提升，自動擴充至您所需的輸送量單位上限。 自動擴充提供下列優點：

- 能視需求進行相應增加的有效擴充機制。
- 自動擴充至指定的上限，以避免產生節流問題。
- 透過控制調整的時機和程度，來取得更多擴充上的控制。

## <a name="enable-auto-inflate-on-a-namespace"></a>在命名空間上啟用自動擴充

您可以使用下列其中一種方法，在事件中樞命名空間上啟用或停用自動擴充：

1. [Azure 入口網站](https://portal.azure.com)。
2. Azure Resource Manager 範本。

### <a name="enable-auto-inflate-through-the-portal"></a>透過入口網站啟用自動擴充

您可以在建立事件中樞命名空間時，啟用自動擴充功能：
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

啟用此選項時，您可以於開始時使用較小的輸送量單位，並隨著使用量需求的提升進行相應增加。 擴充的上限並不會立即影響價格，因為價格會依每小時使用的輸送量單位數來計算。

您也可以使用入口網站中 [設定] 窗格上的 [調整] 選項來啟用自動擴充：
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用自動擴充

您可以在 Azure Resource Manager 範本部署期間啟用自動擴充。 例如，將 `isAutoInflateEnabled` 屬性設定為 **true**，並將 `maximumThroughputUnits` 設定為 10。

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

如需完整的範本，請參閱 GitHub 上的[建立事件中樞命名空間並啟用擴充](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) \(英文\) 範本。

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)

