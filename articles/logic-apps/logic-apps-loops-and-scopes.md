---
title: "在工作流程中建立迴圈和範圍，或解除批次 (debatch) 資料 - Azure Logic Apps | Microsoft Docs"
description: "在 Azure Logic Apps 中建立迴圈來逐一查看資料、將動作群組為範圍，或者解除批次資料，以便啟動更多工作流程。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 64b8f414efe8cd886589084f05e04486c9a0d05c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps 迴圈、範圍和解除批次處理
  
Logic Apps 提供數種方法來處理工作流程中的陣列、集合、批次和迴圈。
  
## <a name="foreach-loop-and-arrays"></a>ForEach 迴圈和陣列
  
Logic Apps 可讓您在一組資料上進行迴圈，並為每個項目執行動作。  透過 `foreach` 動作可在集合上進行迴圈。  在設計工具中，您可以新增一個 for each 迴圈。  在選取您希望反覆查看的陣列之後，就可以新增動作。  您可以每個 foreach 迴圈中新增多個動作。  一旦進入迴圈之後，您就可以開始指定應針對陣列的每個值執行哪些動作。

  此範例會針對每個包含 'microsoft.com' 的電子郵件地址傳送電子郵件。 如果使用程式碼檢視，您就可以指定 for each 迴圈，如下列範例所示：

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  `foreach` 動作可以反覆查看包含數千個實體的陣列。  預設會平行執行反覆運算。  如需陣列和並行限制的詳細資訊，請參閱[限制與設定](logic-apps-limits-and-config.md)。

### <a name="sequential-foreach-loops"></a>循序 ForEach 迴圈

若要啟用要循序執行的 foreach 迴圈，應該新增 `Sequential` 作業選項。

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until 迴圈
  
  您可以執行某個動作或一系列動作，直到符合某個條件為止。  使用 until 迴圈的最常見案例是呼叫端點，直到您取得所需的回應為止。  在設計工具中，您可以指定要新增一個 until 迴圈。  在迴圈中新增動作之後，您就可以設定結束條件以及迴圈限制。
  
  這個範例會呼叫 HTTP 端點，直到回應主體具有「已完成」的值為止。  它會在符合下列其中一種情況時完成： 
  
  * HTTP 回應具有「已完成」的狀態
  * 已嘗試 1 小時的時間
  * 已執行過 100 次的迴圈
  
  如果使用程式碼檢視，您就可以指定 until 迴圈，如下列範例所示：
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn 和解除批次處理

觸發程序有時可能會接收到您想要解除批次處理並針對每個項目啟動工作流程的項目陣列。  透過 `spliton` 命令即可完成解除批次處理。  根據預設，如果您的觸發程序 Swagger 指定的承載是一個陣列，即會新增 `spliton`。 `spliton` 命令會按照陣列中的每個項目啟動一項執行。  SplitOn 只能新增至可以手動設定或覆寫的觸發程序。 您無法具有 `spliton` 並同時實作同步回應模式。  若所呼叫的任何工作流程除了 `spliton` 之外還有 `response` 動作，則會以非同步方式執行並傳送立即 `202 Accepted` 回應。  

  此範例會收到項目陣列，並在每一列上解除批次處理。 SplitOn 可以指定於程式碼檢視中，如下列範例所示：

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>範圍

可能可以使用 scope 來將一系列動作群組在一起。  scope 適合用來實作例外狀況處理。  在設計工具中，您可以新增範圍，並開始在其內部新增任何動作。  您可以在程式碼檢視中定義範圍，如下列範例所示︰


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
