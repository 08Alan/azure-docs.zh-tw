---
title: "如何在 Azure Data Factory 中建立排程觸發程序 |Microsoft 文件"
description: "了解如何建立根據排程執行管線的 Azure Data Factory 中的觸發程序。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>如何建立根據排程執行管線的觸發程序
本文章提供建立、 啟動及監視觸發程序的步驟和排程觸發程序的相關資訊。 其他類型的觸發程序，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

### <a name="schedule-trigger-json-definition"></a>排程觸發程序 JSON 定義
當您建立排程觸發程序時，您可以指定排程與循環使用 JSON，如本節中的範例所示。

若要讓排程觸發程序開始執行的管線，包括觸發程序定義中的特定管線的管線參考。 管道和觸發程序具有多對多關聯性。 多個觸發程序可以啟動單一管道。 單一觸發程序可以啟動多個管道。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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
>  **parameters** 屬性是**管線**內的必要屬性。 即使您的管線不接受任何參數，屬性仍需包含空的 json，因為參數必須存在。


### <a name="overview-schedule-trigger-schema"></a>概觀： 排程觸發程序結構描述
下表提供與觸發程序中的週期和排程相關之主要元素的高階概觀：

JSON 屬性 |     說明
------------- | -------------
startTime | startTime 是日期時間。 在簡單的排程中，startTime 是第一次執行。 在複雜的排程中，觸發程序一到 startTime 就會啟動。
EndTime | 指定觸發程序的結束日期時間。 觸發程序在此時間之後不會執行。 已過去的 endTime 無效。 這是選擇性的屬性。
timeZone | 目前支援僅 UTC。
週期 | recurrence 物件指定觸發程序的週期規則。 recurrence 物件支援的元素：frequency、interval、endTime、count 和 schedule。 如果定義 recurrence，則 frequency 為必要元素，而 recurrence 的其他元素則為選用元素。
frequency | 代表觸發程序一再執行的頻率單位。 支援的值為：`minute`、`hour`、`day`、`week` 或 `month`。
interval | interval 是正整數。 代表用來決定觸發程序執行頻率的頻率間隔。 比方說，如果 interval 為 3，而 frequency 為 "week"，則觸發程序每隔 3 週重複執行一次。
schedule | 具有指定頻率的觸發程序會根據週期排程來改變其週期。 schedule 包含根據分鐘數、小時數、星期幾數、月日數和週數的修改。


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>概觀： 排程觸發程序結構描述預設值、 限制和範例

JSON 名稱 | 值類型 | 必要？ | 預設值 | 有效值 | 範例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | 字串 | 是 | None | ISO 8601 日期時間 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
週期 | Object | 是 | None | Recurrence 物件 | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | 數字 | 否 | 1 | 1 到 1000。 | ```"interval":10```
EndTime | 字串 | 是 | None | 代表未來時間的日期時間值 | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Object | 否 | None | Schedule 物件 | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>深入探討：startTime
下表擷取 startTime 如何控制觸發程序的執行方式：

startTime 值 | 週期性無排程 | 週期性有排程
--------------- | --------------------------- | ------------------------
開始時間已過去 | 計算開始時間之後的第一個未來執行時間，並在該時間執行。<p>根據從上次執行時間算出的時間執行後續的執行作業。</p><p>請參閱本表後面的範例。</p> | 觸發程序「一到」指定的開始時間即啟動。 第一次執行是根據從開始時間計算的排程。 <p>根據週期排程執行後續的執行作業</p>
開始時間在未來或現在 | 在指定的開始時間執行一次。 <p>根據從上次執行時間算出的時間執行後續的執行作業。</p> | 觸發程序「一到」指定的開始時間即啟動。 第一次執行是根據從開始時間計算的排程。<p>根據週期排程執行後續的執行作業。</p>

我們來看看一個範例情況：startTime 已過去，週期性，但無排程。 假設目前的時間是 `2017-04-08 13:00`，startTime 是 `2017-04-07 14:00`，而 recurrence 是每兩天 (以 frequency: day 和 interval: 2 定義)。請注意，startTime 在目前時間之前，已過去。

根據這些條件，第一次執行是在 `2017-04-09 at 14:00`。 排程器引擎會從開始時間計算執行週期。 過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。 因此，在此情況下，startTime 為 `2017-04-07 at 2:00pm`，而下一個執行個體是在該時間的 2 天後，即 `2017-04-09 at 2:00pm`。

第一次執行時間相同，即使 startTime 為 `2017-04-05 14:00` 或 `2017-04-01 14:00` 也一樣。 第一次執行之後，就使用排程來計算後續執行作業。 因此，分別是在 `2017-04-11 at 2:00pm`、接著是 `2017-04-13 at 2:00pm`，然後在 `2017-04-15 at 2:00pm` 等等。

最後，當觸發程序具有排程時，如果未在排程中設定小時及/或分鐘，則會分別預設為第一次執行的小時及/或分鐘。

### <a name="deep-dive-schedule"></a>深入探討：排程
一方面，排程可以限制觸發程序的執行次數。 例如，如果設有 "month" 頻率的觸發程序具有只在月底 31 日執行的排程，則此觸發程序只會在有第 31 天的月份執行。

另一方面，排程也可以增加觸發程序的執行次數。 例如，如果搭配 "month" 頻率的觸發程序具有在當月 1 日及 2 日執行的排程，則此觸發程序會在當月 1 日和 2 日執行，而不是一個月只執行一次。

如果指定多個 schedule 元素，則評估的順序是從最大到最小 – 週數、月日、星期幾、小時和分鐘。

下表詳細說明 schedule 元素：


JSON 名稱 | 說明 | 有效值
--------- | ----------- | ------------
minutes | 一小時內觸發程序執行的分鐘數。 | <ul><li>整數 </li><li>一連串整數</li></ul>
hours | 一天內觸發程序執行的小時數。 | <ul><li>整數 </li><li>一連串整數</li></ul>
weekDays | 觸發程序執行的星期幾。 只能搭配 weekly 頻率指定。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday 或 Sunday</li><li>任何值的陣列 (最大陣列大小為 7)</li></p>不區分大小寫</p>
monthlyOccurrences | 決定在當月哪幾天執行觸發程序。 只能搭配 monthly 頻率指定。 | monthlyOccurence 物件的陣列︰`{ "day": day,  "occurrence": occurence }`。 <p> day 是觸發程序執行的星期幾，例如，`{Sunday}` 是當月的每個星期日。 必要。<p>Occurrence 是當月第幾個星期幾，例如 `{Sunday, -1}` 是當月的最後一個星期日。 選用。
monthDays | 觸發程序執行的月日。 只能搭配 monthly 頻率指定。 | <ul><li><= -1 和 >= -31 的任何值</li><li>>= 1 和 <= 31 任何值</li><li>值的陣列</li>


## <a name="examples-recurrence-schedules"></a>範例：週期排程
本節提供週期排程的範例 – 焦點放在 schedule 物件和其子元素。

範例排程假設 interval 設為 1。 另外也根據排程中的設定來假設正確頻率 – 例如，您在排程中不能使用頻率 "day"，也不能有 "monthDays" 修改。 上一節的表格已提及這些限制。

範例 | 說明
------- | -----------
`{"hours":[5]}` | 在每天上午 5 點執行
`{"minutes":[15], "hours":[5]}` | 在每天上午 5:15 執行
`{"minutes":[15], "hours":[5,17]}` | 在每天上午 5:15 和下午 5:15 執行
`{"minutes":[15,45], "hours":[5,17]}` | 在每天上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行
`{"minutes":[0,15,30,45]}` | 每隔 15 分鐘執行一次
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每小時執行一次。 此觸發程序每小時執行一次。 如果指定 startTime，由它控制分鐘，如果未指定，則由建立時間控制。 比方說，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，則觸發程序會在 00:25、01:25、02:25、…、23:25 執行。 排程相當於 frequency 為 "hour"、interval 為 1 且沒有 schedule 的觸發程序。 差別在於此排程也可以搭配不同的 frequency 和 interval 使用，以建立其他觸發程序。 例如，如果 frequency 為 "month"，則排程只會一個月執行一次，而不是每天執行 (如果 frequency 為 "day")。
`{"minutes":[0]}` | 在每小時整點執行。 此觸發程序也會每小時執行一次，但在整點執行 (例如上午 12 點、上午 1 點、上午 2 點等等)。 這相當於 frequency 為 "hour"、startTime 為零分鐘且沒有 schedule (如果 frequency 為 "day") 的觸發程序，但如果 frequency 為 "week" 或 "month"，則排程會分別在一週某一天或一個月某一天執行。
`{"minutes":[15]}` | 在每小時 15 分執行。 每小時執行，開始於上午 00:15、上午 1:15、上午 2:15 等等，並結束於下午 10:15 和下午 11:15。
`{"hours":[17], "weekDays":["saturday"]}` | 在每週星期六下午 5 點執行
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三、星期五下午 5 點執行
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三、星期五下午 5:15 和 5:45 執行
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每隔 15 分鐘執行一次
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日上午 9 點與下午 4:45 之間每隔 15 分鐘執行一次
`{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四指定的開始時間執行。
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每個月的第 28 天上午 6 點執行 (假設 frequency 為 month)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在當月最後一天上午 6 點執行。 如果您想要在當月最後一天執行觸發程序，請使用 -1，而不是 28、29、30 或 31。
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每個月第一天和最後一天上午 6 點執行
`{monthDays":[1,14]}` | 在每個月第一天和第十四天指定的開始時間執行。
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五上午 5 點執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五指定的開始時間執行。
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每個月倒數第三個星期五的開始時間執行
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五上午 5:15 執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五指定的開始時間執行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每個月第五個星期五的開始時間執行。 如果一個月沒有第五個星期五，則管道不會執行，因為它已排定只在第五個星期五執行。  如果您想要在當月最後一個出現的星期五執行觸發程序，請考慮在 occurrence 中使用 -1 而不是 5。
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在當月最後一個星期五每隔 15 分鐘執行一次。
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每個月第三個星期三上午 5:15、上午 5:45、下午 5:15 和下午 5:45 執行。


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節說明如何使用 Azure PowerShell 來建立、 啟動及監視的排程器觸發程序。 如果您想要查看此範例的工作，會先經過[快速入門： 建立使用 Azure PowerShell 的 data factory](quickstart-create-data-factory-powershell.md)。 然後，將下列程式碼加入至 main 方法，後者則會建立並啟動每隔 15 分鐘執行一次的排程觸發程序。 觸發程序都與管線 (**Adfv2QuickStartPipeline**) 所建立的快速入門的步驟一部分。

1. 建立名為 MyTrigger.json C:\ADFv2QuickStartPSH\ 資料夾具有下列內容中的 JSON 檔案：

    > [!IMPORTANT]
    > 設定**startTime**到目前的 UTC 時間和**endTime**為一小時超過目前的 UTC 時間儲存 JSON 檔案之前。

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    在 JSON 片段：
    - **類型**觸發程序設定為**ScheduleTrigger**。
    - **頻率**設**分鐘**和**間隔**設**15**。 因此，觸發程序執行管線的開始和結束時間之間每隔 15 分鐘。
    - **EndTime**是一小時之後**startTime**，因此觸發程序執行管線 15 分鐘、 30 分鐘，並後 startTime 45 分鐘。 請務必更新至目前的 UTC 時間和結束時間為過去的開始時間的一小時的開始時間。  
    - 觸發程序相關聯**Adfv2QuickStartPipeline**管線。 若要關聯多個管線與觸發程序，新增更多**pipelineReference**區段。
    - 快速入門中的管線會採用兩個**參數**。 因此，您會從觸發程序傳遞這些參數的值。
2. 建立觸發程序使用**組 AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. 確認觸發程序狀態為**已停止**使用**Get AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. 使用啟動觸發程序**開始 AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. 確認觸發程序狀態為**已啟動**使用**Get AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  取得觸發程序執行，使用 PowerShell 來**Get AzureRmDataFactoryV2TriggerRun** cmdlet。 若要取得觸發程序執行的相關資訊，請執行下列命令會定期： 更新**TriggerRunStartedAfter**和**TriggerRunStartedBefore**值，以符合觸發程序定義中的值.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>使用 .NET SDK
本節說明如何使用.NET SDK 來建立、 啟動及監視觸發程序。 如果您想要查看使用這個程式碼時，會先經過[建立 data factory，使用.NET SDK 快速入門](quickstart-create-data-factory-dot-net.md)。 然後，將下列程式碼加入至 main 方法，後者則會建立並啟動每隔 15 分鐘執行一次的排程觸發程序。 觸發程序都與管線 (**Adfv2QuickStartPipeline**) 所建立的快速入門的步驟一部分。

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

若要監視執行的觸發程序，加入下列程式碼的最後一個以外`Console.WriteLine`陳述式：

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>使用 Python SDK
本節說明如何使用 Python SDK 來建立、 啟動及監視觸發程序。 如果您想要查看使用這個程式碼時，會先經過[建立 data factory 使用 Python SDK 快速入門](quickstart-create-data-factory-python.md)。 然後，加入下列程式碼區塊之後 python 指令碼的 監視管線執行 「 程式碼區塊。 此程式碼會建立排程觸發程序執行每隔 15 分鐘之間指定的開始和結束時間。 更新 start_time 為目前的 UTC 時間，然後為目前的 UTC 時間超過一小時 end_time。

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
您可以使用 Azure Resource Manager 範本來建立觸發程序。 如需逐步指示，請參閱[建立 Azure data factory 使用資源管理員範本](quickstart-create-data-factory-resource-manager-template.md)。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>傳遞到管線的觸發程序開始時間
在第 1 版中，Azure Data Factory 支援使用 SliceStart/SliceEnd/WindowStart/WindowEnd 系統變數讀取或寫入分割的資料。 在第 2 版中，您可以透過使用管線參數，以觸發程序的開始時間/已排程的時間作為參數的值來達成此行為。 在下列範例中，觸發程序的排程的時間做為值傳遞給管線參數 scheduledRunTime。

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
如需詳細資訊，請參閱[如何讀取或寫入資料分割資料](how-to-read-write-partitioned-data.md)。

## <a name="next-steps"></a>後續步驟
如需觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
