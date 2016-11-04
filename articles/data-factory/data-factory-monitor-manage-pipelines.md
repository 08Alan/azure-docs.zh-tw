---
title: 監視和管理 Azure Data Factory 管線
description: 了解如何使用 Azure 入口網站和 Azure PowerShell 監視並管理您建立的 Azure 資料處理站和管線。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: spelluru

---
# 監視和管理 Azure Data Factory 管線
> [!div class="op_single_selector"]
> * [使用 Azure 入口網站/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [使用監視及管理應用程式](data-factory-monitor-manage-app.md)
> 
> 

Data Factory 服務提供一個可靠且完整的儲存、處理和資料移動服務檢視。此服務可提供您監視儀表板說明供您執行下列作業︰

* 快速評估端對端資料管線健全狀態。
* 指出問題所在，並視需要採取修正動作。
* 追蹤資料歷程。
* 在您的任何來源間追蹤資料之間的關聯性。
* 檢視作業執行、系統健全狀態和相依性的完整歷程記錄。

本文描述如何監視、管理和偵錯您的管線。同時也會提供如何建立警示和取得失敗通知的詳細資訊。

## 了解管線和活動狀態
您可以使用 Azure 入口網站執行下列操作：

* 以圖表形式檢視 Data Factory
* 檢視管線中的活動
* 檢視輸入和輸出資料集
* 不勝枚舉。

本節也提供配量從某個狀態轉換至另一個狀態的方法。

### 瀏覽至您的 Data Factory
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [全部瀏覽]，選取 [資料處理站]。
   
   ![全部瀏覽 -> 資料處理站](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
   
   您應該會在 [Data Factory] 刀鋒視窗中看到所有 Data Factory。
3. 在 Data Factory 刀鋒視窗中，選取您感興趣的 Data Factory，接著您應該會看到該 Data Factory 的首頁 (**Data Factory** 刀鋒視窗)。
   
    ![Data Factory 刀鋒視窗](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Data Factory 的圖表檢視
Data Factory 的圖表檢視提供單一窗格，可用來監視和管理 Data Factory 及其資產。

若要查看 Data Factory 的圖表檢視，請按一下 Data Factory 首頁上的 [圖表]。

![圖表檢視](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

您可以將圖表配置放大、縮小、縮放至適當比例、放大到 100% 和鎖定，以及自動定位管線和資料表。您也可以查看資料歷程資訊 (顯示所選取項目的上游和下游項目)。

### 管線中的活動
1. 在管線上按一下滑鼠右鍵，然後按一下 [開啟管線]，就能查看所有管線中的活動，以及活動的輸入和輸出資料集。當您的管線有超過一個的活動且您想了解單一管線的作業歷程時，這個功能會非常有用。
   
    ![開啟管線功能表](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)
2. 在下列範例中，您會看到管線中的兩個活動及其輸入和輸出。本管線範例內的兩個活動名為 **JoinData** HDInsight Hive 活動類型和 **EgressDataAzure** 複製活動類型。
   
    ![管線中的活動](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. 您可以按一下左上角階層連結中的 Data Factory 連結，瀏覽回 Data Factory 首頁。
   
    ![瀏覽回到 Data Factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### 管線中每個活動的檢視狀態
您可以藉由檢視活動所產生的任何資料集的狀態，查看活動的目前狀態。

例如：在下列範例中，**BlobPartitionHiveActivity** 已順利執行並產生名為 **PartitionedProductsUsageTable** 的資料集，其狀態為**就緒**。

![管線的狀態](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

按兩次圖表檢視中的 **PartitionedProductsUsageTable**，會展示管線中各種不同的活動執行時所產生的所有配量。您可以看到 **BlobPartitionHiveActivity** 過去八個月來每個月都執行成功，所產生的配量都處於**就緒**狀態。

Data Factory 中的資料集配量可以有下列狀態之一：

<table>
<tr>
    <th align="left">State</th><th align="left">子狀態</th><th align="left">說明</th>
</tr>
<tr>
    <td rowspan="8">等候</td><td>ScheduleTime</td><td>尚未到達執行配量的時間。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>上游相依項目尚未就緒。</td>
</tr>
<tr>
<td>ComputeResources</td><td>計算資源無法使用。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>所有活動執行個體都忙於執行其他配量。</td>
</tr>
<tr>
<td>ActivityResume</td><td>活動已暫停，要繼續之後才能執行配量。</td>
</tr>
<tr>
<td>Retry</td><td>會重試活動執行。</td>
</tr>
<tr>
<td>驗證</td><td>驗證尚未啟動。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>正在等待重試驗證。</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>驗證進行中。</td>
</tr>
<td></td>
<td>正在處理配量。</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>執行花費的時間超過活動所允許的時間。</td>
</tr>
<tr>
<td>Canceled</td><td>被使用者動作取消。</td>
</tr>
<tr>
<td>驗證</td><td>驗證失敗。</td>
</tr>
<tr>
<td></td><td>無法產生和/或驗證配量。</td>
</tr>
<td>就緒</td><td></td><td>配量已就緒，可供取用。</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>未處理配量。</td>
</tr>
<tr>
<td>None</td><td></td><td>過去存在不同狀態的配量，但已重設。</td>
</tr>
</table>



按一下 [**最近更新的配量**] 刀鋒視窗中的配量項目，即可檢視有關配量的詳細資訊。

![配量的詳細資料](./media/data-factory-monitor-manage-pipelines/slice-details.png)

若已多次執行配量，則您會在 [活動執行] 清單中看到多個資料列。

![配量的活動執行](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

您可按一下 [**活動執行**] 清單中的執行項目，檢視有關活動執行的詳細資訊。此清單會顯示所有記錄檔，且如果有錯誤訊息的話，也會一併展示。這個功能非常實用，您可以檢視和偵錯記錄檔而不必離開您的 Data Factory。

![活動執行詳細資料](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

若配量不是處於 [**就緒**] 狀態，您可以在 [**未就緒的上游配量**] 清單中看到未就緒且阻礙目前配量執行的上游配量。當您的配量處於 [等候] 狀態且您想要了解配量等候的上游相依項目時，此功能相當有用。

![尚未就緒的上游配量](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### 資料集狀態圖表
一旦您部署 Data Factory 且管線具有有效的使用中週期，則資料集配量會從某個狀態轉換到另一種狀態。目前配量狀態遵循下列的狀態圖表：

![狀態圖表](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Data Factory 內的資料集狀態轉換流程有下列階段：等候中 -> 進行中/進行中 (驗證中) -> 就緒/失敗

處於 [**等候**] 狀態的配量在執行前，會先開始進行符合前置條件的動作。接著活動開始執行，配量進入 [進行中] 狀態。活動執行可能成功或失敗。根據執行的結果，配量會標示為 [就緒] 或 [失敗]。

您可以重設配量，以從 [就緒] 或 [失敗] 狀態返回 [等候] 狀態。您也可以將配量狀態標記為 [略過]，這會防止活動執行且不會處理該配量。

## 管理管線
您可以使用 Azure Powershell 管理您的管線。例如，您可以執行 Azure PowerShell Cmdlet 來暫停和繼續執行管線。

### 暫停及繼續管線
您可以使用 **Suspend-AzureRmDataFactoryPipeline** Powershell Cmdlet 來暫停/暫止管線。若您在問題獲得解決之前不想執行管線，此 Cmdlet 非常有用。

例如：在下列螢幕擷取畫面中，**productrecgamalbox1dev** Data Factory 內的 **PartitionProductsUsagePipeline** 發現問題，因此我們想暫止管線。

![暫止的管線](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

若要暫停管線，請執行下列 PowerShell 命令。

    Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

例如：

    Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

一旦 **PartitionProductsUsagePipeline** 的問題修正後，只要執行下列 PowerShell 命令，暫止的管線就可以繼續執行。

    Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

例如：

    Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## 偵錯管線
Azure Data Factory 透過 Azure 入口網站和 Azure PowerShell 提供許多功能，可用來偵錯和疑難排解管線。

### 尋找管線中的錯誤
如果管線中的活動執行失敗，管線所產生的資料集會因為該失敗而處於錯誤狀態。您可以使用下列方法，在 Azure Data Factory 中偵錯和疑難排解錯誤。

#### 使用 Azure 入口網站偵錯錯誤：
1. 在 Data Factory 首頁，按一下 [**資料集**] 磚上的 [**發生錯誤**]。
   
   ![發生錯誤的資料集磚](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2. 在 [**出現錯誤的資料集**] 刀鋒視窗中，按一下您感興趣的資料表。
   
   ![[發生錯誤的資料集] 刀鋒視窗](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3. 在 [資料表] 刀鋒視窗中，按一下 [狀態] 設為 [失敗] 的問題配量。
   
   ![含有問題配量的資料表刀鋒視窗](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4. 在 [**資料配量**] 刀鋒視窗中，按一下失敗的活動執行。
   
   ![發生錯誤的資料配量](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5. 在 [**活動執行詳細資料**] 刀鋒視窗中，您可以下載與 HDInsight 處理相關聯的檔案。按一下 Status/stderr 中的 [下載] 以下載包含錯誤詳細資料的錯誤記錄檔。
   
   ![含有錯誤的活動執行詳細資料刀鋒視窗](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### 使用 PowerShell 偵錯錯誤
1. 啟動 **Azure PowerShell**。
2. 執行 **Get-AzureRmDataFactorySlice** 命令來查看配量及其狀態。您應該會看到有以下狀態的配量：[**失敗**]。
   
     Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
   
   例如：

        Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

    將 **StartDateTime** 取代成您針對 Set-AzureRmDataFactoryPipelineActivePeriod 所指定的 StartDateTime 值。
1. 現在，執行 **Get-AzureRmDataFactoryRun** Cmdlet，以取得關於此配量之活動執行的詳細資料。
   
        Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
        DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
        <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
   
    例如：

        Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

    StartDateTime 的值就是您在上一步驟記下的錯誤/問題配量的開始時間。date-time 應該用雙引號括住。
1. 您應該會看到關於此錯誤的詳細資料 (如下所示) 輸出：
   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    TableName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
   
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
2. 您可以使用在輸出中看見的 ID 值執行 **Save-AzureRmDataFactoryLog** Cmdlet，並在 Cmdlet 中使用 **-DownloadLogsoption** 來下載記錄檔。
   
   Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"

## 重新執行管線中的失敗
### 使用 Azure 入口網站
一旦您對管線中的失敗進行疑難排解和偵錯，您可以瀏覽到錯誤配量並按一下命令列上的 [執行] 按鈕，重新執行失敗。

![重新執行失敗的配量](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

萬一原則失敗而導致配量驗證失敗 (例如：沒有可用資料)，您可以修正失敗並重新驗證，方法是按一下命令列上的 [**驗證**] 按鈕。![修正錯誤並進行驗證](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### 使用 Azure PowerShell
您可以使用 Set-AzureRmDataFactorySliceStatus Cmdlet 來重新執行失敗。如需該 Cmdlet 的語法及其他詳細資料，請參閱 [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) 主題。

**範例：**下列範例把 Azure Data Factory 'WikiADF' 中 'DAWikiAggregatedData' 資料表的所有配量狀態都設為 'Waiting'。

UpdateType 已設為 UpstreamInPipeline，這代表資料表中每個配量的狀態以及所有相依 (上游) 資料表的狀態都設為 "Waiting"。 此參數的另一個可能值為 "Individual"。

    Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## 建立警示
當建立、更新或刪除 Azure 資料時 (例如 Data Factory)，Azure 會記錄使用者事件。您可以對這些事件建立警示。Data Factory 可讓您擷取各種度量並建立度量警示。我們建議您使用即時監視事件及做為歷程用途的度量。

### 事件警示
Azure 事件可讓您深入了解 Azure 資源的情況。當建立、更新或刪除 Azure 資料時 (例如 Data Factory)，Azure 會記錄使用者事件。使用 Azure Data Factory 時，下列情況會產生事件：

* 建立/更新/刪除 Azure Data Factory。
* 資料處理 (稱為「回合」) 開始/完成。
* 建立和移除隨選 HDInsight 叢集。

您可以針對這些使用者事件建立警示，並設定它們傳送電子郵件通知給訂用帳戶的管理員和共同管理員。此外，您還可以指定使用者的其他電子郵件地址，當條件符合時，這些使用者需要收到電子郵件通知。此功能在您想要取得有關失敗的通知且不想要持續監視您的 Data Factory 時會非常有用。

> [!NOTE]
> 入口網站目前無法顯示事件警示。使用[監視及管理應用程式](data-factory-monitor-manage-app.md)來查看所有警示。
> 
> 

#### 指定警示定義：
若要指定警示定義，您需要建立 JSON 檔案來描述您想要接獲通知的作業。在下列範例中，警示會針對 RunFinished 作業傳送電子郵件通知。具體而言，當 Data Factory 中完成一個回合，而且執行失敗時 (Status = FailedExecution)，就會傳送電子郵件通知。

    {
        "contentVersion": "1.0.0.0",
         "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters": {},
        "resources": 
        [
            {
                "name": "ADFAlertsSlice",
                "type": "microsoft.insights/alertrules",
                "apiVersion": "2014-04-01",
                "location": "East US",
                "properties": 
                {
                    "name": "ADFAlertsSlice",
                    "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                    "isEnabled": true,
                    "condition": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                        "dataSource": 
                        {
                            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                            "operationName": "RunFinished",
                            "status": "Failed",
                            "subStatus": "FailedExecution"   
                        }
                    },
                    "action": 
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails": [ "<your alias>@contoso.com" ]
                    }
                }
            }
        ]
    }

在 JSON 定義中，如果不想接獲特定失敗的通知，您可以移除 **subStatus**。

此範例會為您的訂用帳戶中所有 Data Factory 設定警示。如果您想要為特定 Data Factory 設定警示，可以在 **dataSource** 指定 Data Factory 的 **resourceUri**：

    "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

下列表格提供可用作業與狀態 (及子狀態) 的清單。

| 作業名稱 | 狀態 | 子狀態 |
| --- | --- | --- |
| RunStarted |已啟動 |啟動中 |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |已啟動 | |
| OnDemandClusterCreateSuccessful |Succeeded | |
| OnDemandClusterDeleted |Succeeded | |

如需範例中所使用之 JSON 元素的詳細資料，請參閱[建立警示規則](https://msdn.microsoft.com/library/azure/dn510366.aspx)。

#### 部署警示
如果要部署警示，請使用 Azure PowerShell Cmdlet：**New-AzureRmResourceGroupDeployment**，如下列範例所示：

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

成功完成資源群組部署之後，您會看到下列訊息：

    VERBOSE: 7:00:48 PM - Template is valid.
    WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
    Please update scripts to remove this parameter.
    VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
    VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :

> [!NOTE]
> 您可以使用[建立警示規則](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST API 來建立警示規則。JSON 承載類似於 JSON 範例。
> 
> 

#### 擷取 Azure 資源群組部署的清單
如果要擷取已部署的 Azure 資源群組部署清單，請使用 Cmdlet：**Get-AzureRmResourceGroupDeployment**，如下列範例所示：

    Get-AzureRmResourceGroupDeployment -ResourceGroupName adf

    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :


#### 使用者事件疑難排解
* 您可以看到按一下 [作業] 圖格後所產生的所有事件，而且也可以在 [事件] 刀鋒視窗中針對這些作業設定顯示警示：
  
    ![Operations](./media/data-factory-monitor-manage-pipelines/operations.png)
* 如需可用於新增/取得/移除警示的 PowerShell Cmdlet 資訊，請參閱 [Azure Insight Cmdlet](https://msdn.microsoft.com/library/mt282452.aspx) 一文。以下是一些關於使用 **Get AlertRule** Cmdlet 的範例：

        PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det

                Properties :
                Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
                Condition   :
                DataSource :
                EventName             :
                Category              :
                Level                 :
                OperationName         : RunFinished
                ResourceGroupName     :
                ResourceProviderName  :
                ResourceId            :
                Status                : Failed
                SubStatus             : FailedExecution
                Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
                Condition      :
                Description : One or more of the data slices for the Azure Data Factory has failed processing.
                Status      : Enabled
                Name:       : ADFAlertsSlice
                Tags       :
                $type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
                Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
                Location   : West US
                Name       : ADFAlertsSlice

        PS C:\> Get-AlertRule -res $resourceGroup

                Properties : Microsoft.Azure.Management.Insights.Models.Rule
                Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
                Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
                Location   : West US
                Name       : FailedExecutionRunsWest0

                Properties : Microsoft.Azure.Management.Insights.Models.Rule
                Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
                Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
                Location   : West US
                Name       : FailedExecutionRunsWest3

        PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0

                Properties : Microsoft.Azure.Management.Insights.Models.Rule
                Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
                Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
                Location   : West US
                Name       : FailedExecutionRunsWest0

    執行下列 get-help 命令，以查看關於 Get-AlertRule Cmdlet 的詳細資訊和範例。

        get-help Get-AlertRule -detailed 
        get-help Get-AlertRule -examples


* 如果您在入口網站刀鋒視窗上看到產生警示的事件但沒有收到電子郵件通知，請檢查指定的電子郵件地址是否設定為接收來自外部寄件者的電子郵件。警示的電子郵件可能遭到您的電子郵件設定封鎖。

### 度量的警示
Data Factory 可讓您擷取各種度量並建立度量警示。您可以針對您 Data Factory 配量的下列度量進行監視和建立警示。

* 失敗的執行
* 成功的執行

這些度量很有幫助，並且可讓您在其 Data Factory 取得整體失敗和成功執行的概觀。每次有配量執行時就會發出度量。整點時，這些度量會彙總並推送至儲存體帳戶。因此，若要啟用度量，請設定儲存體帳戶。

#### 啟用度量：
若要啟用度量，請從 Data Factory 的刀鋒視窗按一下下列選項：

[**監視**] -> [**度量**] -> [**診斷設定**] -> [**診斷**]

在 [**診斷**] 刀鋒視窗中，按一下 [**啟用**]，然後選取儲存體帳戶並儲存。

![啟用度量](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

儲存之後，在監視刀鋒視窗中可能最多可以看見度量一小時，因為度量每小時彙總一次。

### 設定度量警示：
若要設定度量警示，請從 Data Factory 刀鋒視窗按一下下列選項：[監視] -> [度量] -> [新增警示] -> [新增警示規則]。

填入警示規則的詳細資料、指定電子郵件並按一下 [確定]。

![設定度量警示](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

完成之後，您應該會看到警示規則磚上啟用新的警示規則，如下所示：

![啟用的警示規則](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

恭喜！ 您已設定您的第一個度量警示。現在開始，每次警示規則在指定的時間間隔內進行比對時，您應該就會收到通知。

### 警示通知：
一旦警示規則與條件相符，您應該會收到警示啟用的電子郵件。一旦問題獲得解決且警示條件不再符合任何規則，您就會收到警示已解決的電子郵件。

此行為不同於事件，其會針對警示規則相符的每個失敗傳送通知。

### 使用 PowerShell 部署警示
您可以使用部署事件警示的方法來部署度量警示。

**警示定義：**

    {
        "contentVersion" : "1.0.0.0",
        "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "parameters" : {},
        "resources" : [
        {
                "name" : "FailedRunsGreaterThan5",
                "type" : "microsoft.insights/alertrules",
                "apiVersion" : "2014-04-01",
                "location" : "East US",
                "properties" : {
                    "name" : "FailedRunsGreaterThan5",
                    "description" : "Failed Runs greater than 5",
                    "isEnabled" : true,
                    "condition" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                        "dataSource" : {
                            "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                            "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                            "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
    >/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                            "metricName" : "FailedRuns"
                        },
                        "threshold" : 5.0,
                        "windowSize" : "PT3H",
                        "timeAggregation" : "Total"
                    },
                    "action" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                        "customEmails" : ["abhinav.gpt@live.com"]
                    }
                }
            }
        ]
    }

以適當的值取代範例中的 subscriptionId、resourceGroupName、和 dataFactoryName。

metricName 目前支援兩個值︰

* FailedRuns
* SuccessfulRuns

**部署警示：**

如果要部署警示，請使用 Azure PowerShell Cmdlet：**New-AzureRmResourceGroupDeployment**，如下列範例所示：

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

您應該會在部署成功後看到下列訊息：

    VERBOSE: 12:52:47 PM - Template is valid.
    VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
    VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


    DeploymentName    : FailedRunsGreaterThan5
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 7/27/2015 7:52:56 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           


您也可以使用 **Add-AlertRule** Cmdlet 來部署警示規則。如需詳細資料及範例，請參閱 [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) 主題。

## 將 Data Factory 移至另一個資源群組或訂用帳戶
您可以使用 Data Factory 首頁上的 [移動] 命令列按鈕，將 Data Factory 移至另一個資源群組或訂用帳戶。

![移動 Data Factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

您也可以將任何相關資源 (例如與 Data Factory 相關聯的警示) 連同 Data Factory 一起移動。

![移動資源對話方塊](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

<!----HONumber=AcomDC_0907_2016-->