---
title: "Azure Data Factory 中的管道執行和觸發程序 | Microsoft Docs"
description: "本文提供相關資訊來說明如何在 Azure Data Factory 中依需求或建立觸發程序來執行管道。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: fc34cfbab796c6e1e4cd25ce13dcc63c39c6699d
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory 中的管道執行和觸發程序
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [第 1 版 - 正式運作](v1/data-factory-scheduling-and-execution.md)
> * [第 2 版 - 預覽](concepts-pipeline-execution-triggers.md)

_管線執行_是 Azure Data Factory 第 2 版中的詞彙，定義管線執行的執行個體。 例如，假設您的管線會在上午 8 點、9 點和 10 點執行。 在此情況下，管線會有三個個別的執行 (或管線執行)。 每個管道執行都有唯一的管道執行識別碼，這是唯一定義該特定管道執行的 GUID。 通常將引數傳遞給管線中定義的參數，以具現化管線執行。 有兩種方式可執行管線：手動或使用_觸發程序_。 本文提供兩種管線執行方式的詳細資料。

> [!NOTE]
> 本文適用於第 2 版 Azure Data Fatory (目前為預覽版)。 如果您使用第 1 版 Azure Data Factory (也就是正式運作版 (GA))，請參閱 [Azure Data Factory 第 1 版中的排程和執行](v1/data-factory-scheduling-and-execution.md)。

## <a name="manual-execution-on-demand"></a>手動執行 (依需求)
管線的手動執行也稱為_依需求_執行。

例如，假設您想要執行名為 **copyPipeline** 的簡易管線。 此管線只有單一活動，從 Azure Blob 儲存體的來源資料夾複製到相同儲存體中的目的地資料夾。 下列 JSON 定義將說明此範例管線：

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}
```

在 JSON 定義中，管線採用兩個參數：**sourceBlobContainer** 和 **sinkBlobContainer**。 您可以在執行階段將值傳遞給這些參數。

您可以使用下列方法手動執行管線：
- .NET SDK。
- Azure PowerShell 模組。
- REST API。
- Python SDK。

### <a name="the-rest-api"></a>REST API
下列範例命令說明如何使用 REST API 以手動方式執行管線：  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

如需完整範例，請參閱[快速入門：使用 REST API 建立資料處理站](quickstart-create-data-factory-rest-api.md)。

### <a name="azure-powershell"></a>Azure PowerShell
下列範例命令說明如何使用 Azure PowerShell 以手動方式執行管線：

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

您可以在要求承載的本文中傳遞參數。 在 .NET SDK、Azure Powershell 和 Python SDK 中，您可以在當作引數傳給呼叫的字典中傳入值：

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

回應承載是管道執行的唯一識別碼：

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

如需完整範例，請參閱[快速入門：使用 Azure PowerShell 建立資料處理站](quickstart-create-data-factory-powershell.md)。

### <a name="the-net-sdk"></a>.NET SDK
下列範例呼叫說明如何使用 .NET SDK 以手動方式執行管線：

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

如需完整範例，請參閱[快速入門：使用 .NET SDK 建立資料處理站](quickstart-create-data-factory-dot-net.md)。

> [!NOTE]
> 您可以使用 .NET SDK 從 Azure Functions、您自己的 Web 服務等途徑叫用 Azure Data Factory 管線。

<h2 id="triggers">觸發程序執行</h2>
觸發程序是執行管線的第二種方式。 觸發程序代表一個處理單位，用來決定何時需要啟動管道執行。 目前，Azure Data Factory 支援兩種類型的觸發程序：
- 排程觸發程序：可依時鐘排程來叫用管線的觸發程序。
- 輪轉視窗觸發程序：依定期間隔運作且同時保有狀態的觸發程序。 Azure Data Factory 目前不支援以事件為基礎的觸發程序。 例如，回應檔案送達事件的管線執行觸發程序。

管道和觸發程序具有多對多關聯性。 多個觸發程序可以啟動單一管線，或單一觸發程序可以啟動多個管線。 在下列觸發程序定義中，**pipelines** 屬性會參考由特定觸發程序所觸發的管線清單。 屬性定義包含管線參數的值。

### <a name="basic-trigger-definition"></a>基本觸發程序定義

```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>排程觸發程序
排程觸發程序會依時鐘排程來執行管線。 此觸發程序支援定期和進階行事曆選項。 例如，此觸發程序支援「每週」、「星期一下午 5 點和星期四晚上 9 點」之類的間隔。 排程觸發程序很有彈性，因為它不受限於特定資料集模式，也不會區別時間序列和非時間序列的資料。

如需關於排程觸發程序的詳細資訊和範例，請參閱[建立排程觸發程序](how-to-create-schedule-trigger.md)

## <a name="tumbling-window-trigger"></a>輪轉視窗觸發程序
輪轉視窗觸發程序是可從指定的開始時間定期引發，同時還能保留狀態的一種觸發程序。 輪轉視窗是一系列大小固定、非重疊的連續時間間隔。 如需關於輪轉視窗觸發程序的詳細資訊和範例，請參閱[建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)。

## <a name="schedule-trigger-definition"></a>排程觸發程序定義
在建立排程觸發程序時，您必須使用 JSON 定義指定排程和週期。 

若要讓排程觸發程序啟動管線執行，請在觸發程序定義中包含特定管線的管線參考。 管道和觸發程序具有多對多關聯性。 多個觸發程序可以啟動單一管道。 單一觸發程序可以啟動多個管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
> **parameters** 屬性是 **pipelines** 元素的必要屬性。 如果您的管線未採用任何參數，您就必須為 **parameters** 屬性加入一個空的 JSON 定義。

### <a name="schema-overview"></a>結構描述概觀
下表提供與觸發程序之週期和排程相關的主要結構描述元素概觀：

| JSON 屬性 | 說明 |
|:--- |:--- |
| **startTime** | 日期時間值。 在簡易排程中，**startTime** 屬性的值會套用至第一個發生項目。 在複雜的排程中，觸發程序會在到了指定的 **startTime** 值才啟動。 |
| **endTime** | 觸發程序的結束日期和時間。 觸發程序在指定的結束日期和時間之後便不再執行。 此屬性的值不可以是過去的時間。 <!-- This property is optional. --> |
| **timeZone** | 時區。 目前僅支援 UTC 時區。 |
| **recurrence** | 指定觸發程序之週期規則的 recurrence 物件。 recurrence 物件支援 **frequency**、**interval**、**endTime**、**count** 及 **schedule** 元素。 定義 recurrence 物件時，必須一併定義 **frequency** 元素。 其他 recurrence 物件元素則為選用元素。 |
| **frequency** | 觸發程序重複執行時的頻率單位。 支援的值包括 "minute"、"hour"、"day"、"week" 及 "month"。 |
| **interval** | 代表 **frequency** 值之間隔的整數值，用來決定觸發程序執行的頻率。 例如，如果 **interval** 為 3，而 **frequency** 為 "week"，觸發程序就會每隔 3 週重複執行一次。 |
| **schedule** | 觸發程序的週期排程。 具有指定之 **frequency** 值的觸發程序會根據週期排程來改變其週期。 **schedule** 屬性會根據分鐘、小時、星期幾、月日及週數來修改週期。

### <a name="schedule-trigger-example"></a>排程觸發程序範例

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>結構描述預設值、限制及範例

| JSON 屬性 | 類型 | 必要 | 預設值 | 有效值 | 範例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | 字串 | yes | None | ISO 8601 日期時間 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Object | yes | None | Recurrence 物件 | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | 否 | 1 | 1 到 1,000 | `"interval":10` |
| **endTime** | 字串 | yes | None | 代表未來時間的日期時間值。 | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Object | 否 | None | Schedule 物件 | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime 屬性
下表說明 **startTime** 屬性如何控制觸發程序執行：

| startTime 值 | 週期性無排程 | 週期性有排程 |
|:--- |:--- |:--- |
| 開始時間已過去 | 計算開始時間之後的第一個未來執行時間，並在該時間執行。<br/><br/>根據從上次執行時間算出的時間來執行後續的執行作業。<br/><br/>請參閱本表後面的範例。 | 觸發程序會在「到了」指定的開始時間才啟動。 第一次執行是根據從開始時間算出的排程。<br/><br/>根據週期排程執行後續的執行作業。 |
| 開始時間在未來或現在 | 在指定的開始時間執行一次。<br/><br/>根據從上次執行時間算出的時間來執行後續的執行作業。 | 觸發程序會在「到了」指定的開始時間才啟動。 第一次執行是根據從開始時間算出的排程。<br/><br/>根據週期排程執行後續的執行作業。 |

我們來看看一個範例：當開始時間在過去、具有週期性但無排程時，會發生什麼情況。 假設目前時間是 `2017-04-08 13:00`，開始時間是 `2017-04-07 14:00`，而週期是每隔兩天。 (定義 **recurrence** 值的方式是將 **frequency** 屬性設定為 "day"，並將 **interval**屬性設定為 2)。請注意，**startTime** 值是過去的時間，發生在目前時間之前。

根據這些條件，第一次執行是在 `2017-04-09 at 14:00`。 排程器引擎會從開始時間計算執行週期。 過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。 在此案例中，開始時間是 `2017-04-07 at 2:00pm`，因此下一個執行個體是在該時間的兩天後，亦即 `2017-04-09 at 2:00pm`。

即使 **startTime** 值為 `2017-04-05 14:00` 或 `2017-04-01 14:00`，第一次執行時間仍然相同。 在第一次執行之後，就會使用排程來算出後續的執行時間。 因此，後續的執行依序會發生在 `2017-04-11 at 2:00pm`、`2017-04-13 at 2:00pm`、`2017-04-15 at 2:00pm`，依此類推。

最後，當未在觸發程序的排程中設定小時或分鐘時，將會使用第一次執行的小時或分鐘作為預設值。

### <a name="schedule-property"></a>schedule 屬性
一方面，使用排程可以限制觸發程序的執行次數。 例如，如果將每月執行的觸發程序排定為只在 31 日執行，則該觸發程序將只會在有第 31 天的月份執行。

另一方面，排程也可以增加觸發程序的執行次數。 例如，如果將每月執行的觸發程序排定在月份的 1 日和 2 日執行，該觸發程序就會在月份的第 1 天和第 2 天執行，而不是一個月執行一次。

指定多個 **schedule** 元素時，評估順序會從最大到最小排程設定。 評估會從週數開始，然後依序是月日、星期幾、小時，最後是分鐘。

下表詳細說明 **schedule** 元素：

| JSON 元素 | 說明 | 有效值 |
|:--- |:--- |:--- |
| **minutes** | 一小時內觸發程序執行的分鐘數。 | <ul><li>整數 </li><li>一連串整數</li></ul>
| **hours** | 一天內觸發程序執行的小時數。 | <ul><li>整數 </li><li>一連串整數</li></ul> |
| **weekDays** | 觸發程序在一週中的執行日。 此值只能與 weekly 頻率搭配指定。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday</li><li>日期值陣列 (最大陣列大小為 7)</li><li>日值不區分大小寫</li></ul> |
| **monthlyOccurrences** | 觸發程序在一個月中的執行日。 此值只能與 monthly 頻率搭配指定。 | <ul><li>**monthlyOccurence** 物件的陣列︰`{ "day": day,  "occurrence": occurence }`。</li><li>**day** 屬性是觸發程序在一週中的執行日。 例如，**day** 值為 `{Sunday}` 的 **monthlyOccurrences** 屬性意謂著月份中的每個星期日。 **day** 屬性為必要屬性。</li><li>**occurrence** 屬性係指所指定的 **day** 在月份中出現的位置。 例如，**day** 和 **occurrence** 值為 `{Sunday, -1}` 的 **monthlyOccurrences** 屬性意謂著月份中的最後一個星期日。 **occurrence** 屬性為選用屬性。</li></ul> |
| **monthDays** | 觸發程序在一個月中的執行日。 此值只能與 monthly 頻率搭配指定。 | <ul><li><= -1 和 >= -31 的任何值</li><li>>= 1 和 <= 31 任何值</li><li>值的陣列</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>觸發程序週期排程的範例
本節提供週期排程的範例，並將焦點放在 **schedule** 物件及其元素。

這些範例會假設 **interval** 值為 1，且 **frequency** 值根據排程定義是正確的。 例如，您不能既將 **frequency** 值設定為 "day"，又在 **schedule** 物件中包含 "monthDays"修改。 在上一節的表格中已提到這類限制。

| 範例 | 說明 |
|:--- |:--- |
| `{"hours":[5]}` | 在每天的上午 5:00 執行。 |
| `{"minutes":[15], "hours":[5]}` | 在每天的上午 5:15 執行。 |
| `{"minutes":[15], "hours":[5,17]}` | 在每天的上午 5:15 和下午 5:15 執行。 |
| `{"minutes":[15,45], "hours":[5,17]}` | 在每天的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |
| `{"minutes":[0,15,30,45]}` | 每隔 15 分鐘執行一次。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每小時執行一次。 此觸發程序每小時執行一次。 已指定值時，分鐘會受 **startTime** 值控制。 如果未指定值，分鐘就會受建立時間控制。 例如，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，觸發程序就會在 00:25、01:25、02:25、… 及 23:25 執行。<br/><br/>此排程相當於將觸發程序設定成 **frequency** 值為 "hour"、**interval** 值為 1 且沒有 **schedule**。 此排程可以與不同的 **frequency** 和 **interval** 值搭配使用，以建立其他觸發程序。 例如，當 **frequency** 值為 "month" 時，排程一個月只會執行一次，而不是像 **frequency** 值為 "day" 時會每天執行。 |
| `{"minutes":[0]}` | 在每小時整點執行。 此觸發程序會在每小時整點執行，從上午 12:00 開始，接著在上午 1:00、上午 2:00，依此類推。<br/><br/>此排程相當於將觸發程序設定成 **frequency** 值為 "hour"、**startTime** 值為零分鐘，或沒有 **schedule** 但 **frequency** 值為 "day"。 如果 **frequency** 值為 "week" 或 "month"，排程將只會分別在每週的一天或每個月的一天執行。 |
| `{"minutes":[15]}` | 在每小時的 15 分執行。 此觸發程序會在每小時的 15 分執行，從上午 00:15 開始，接著在上午 1:15、上午 2:15，依此類推，並在下午 11:15 結束。 |
| `{"hours":[17], "weekDays":["saturday"]}` | 在每週星期六的下午 5:00 執行。 |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三及星期五的下午 5:00 執行。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三及星期五的下午 5:15 和 5:45 執行。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每隔 15 分鐘執行一次。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日的上午 9:00 與下午 4:45 之間每隔 15 分鐘執行一次。 |
| `{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四的指定開始時間執行。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每個月的第 28 天上午 6:00 執行 (假設 **frequency** 值為 "month")。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在月份最後一天的上午 6:00 執行。 若要在月份的最後一天執行觸發程序，請使用 -1，而不要使用 28、29、30 或 31。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每個月第一天和最後一天的上午 6:00 執行。 |
| `{monthDays":[1,14]}` | 在每個月第一天和第十四天的指定開始時間執行。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五的上午 5:00 執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五的指定開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每個月倒數第三個星期五的指定開始時間執行。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五的上午 5:15 執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五的指定開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每個月第五個星期五的指定開始時間執行。 如果月份中沒有第五個星期五，管線就不會執行，因為它已排定為只在第五個星期五執行。 若要在月份中最後一個出現的星期五執行觸發程序，請考慮使用 -1 而不是 5 來作為 **occurrence** 的值。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在月份中最後一個星期五每隔 15 分鐘執行一次。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每個月第三個星期三的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |

## <a name="trigger-type-comparison"></a>觸發程序類型比較
輪轉視窗觸發程序和排程觸發程序皆依時間活動訊號運作，所以兩者有何不同？

下表提供輪轉視窗觸發程序與排程觸發程序的比較：

|  | 輪轉&nbsp;視窗&nbsp;觸發程序 | 排程&nbsp;觸發程序 |
|:--- |:--- |:--- |
| **回填&nbsp;案例** | 支援。 管線執行可排程於過去的時間範圍執行。 | 不支援。 管線執行只能排程在目前到未來的期間內執行。 |
| **可靠性** | 100% 可靠性。 管線執行可排程於指定的開始日期之後的所有時間範圍執行，且不間斷。 | 較不可靠。 |
| **重試&nbsp;功能** | 支援。 失敗管線執行的預設重試原則為 0，或是使用者在觸發程序定義中指定的原則。 當管線執行因為並行/伺服器/節流限制而失敗 (也就是狀態碼 400：使用者錯誤、429：太多要求和 500：內部伺服器錯誤) 時，將會自動重試。 | 不支援。 |
| **並行** | 支援。 使用者可以明確設定觸發程序的並行限制。 允許 1 個到最多 50 個已觸發的並行管線執行。 | 不支援。 |
| **系統&nbsp;變數** | 支援使用 **WindowStart** 和 **WindowEnd** 系統變數。 使用者可以存取 `triggerOutputs().windowStartTime` 和 `triggerOutputs().windowEndTime` 作為觸發程序定義中的觸發程序系統變數。 其值會分別作為時間範圍開始時間和時間範圍結束時間。 例如，對於每小時執行一次的輪轉視窗觸發程序，如果時間範圍為凌晨 1 點到凌晨 2 點，則定義為 `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` 和 `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`。 | 不支援。 |
| **管線與觸發程序的關聯性** | 支援一對一關聯性。 只能觸發一個管線。 | 支援多對多關聯性。 多個觸發程序可以啟動單一管道。 單一觸發程序可以啟動多個管道。 | 

## <a name="next-steps"></a>後續步驟
請參閱下列教學課程：

- [快速入門：使用 .NET SDK 建立資料處理站](quickstart-create-data-factory-dot-net.md)
- [建立排程觸發程序](how-to-create-schedule-trigger.md)
- [建立輪轉視窗觸發程序](how-to-create-tumbling-window-trigger.md)
