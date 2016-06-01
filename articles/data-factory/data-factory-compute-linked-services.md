<properties
	pageTitle="計算連結服務 | Microsoft Azure"
	description="了解您可以在 Azure Data Factory 管線中用來轉換/處理資料的計算環境。"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="spelluru"/>

# 計算連結服務

本文說明您可用來處理或轉換資料的各種計算環境。其中還提供在設定將這些計算環境連結至 Azure Data Factory 的連結服務時，Data Factory 所支援的不同組態 (隨選與自備) 的詳細資料。

## 隨選計算環境

在這種組態中，運算環境完全是由 Azure Data Factory 服務管理。Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。您可以建立隨選計算環境的連結服務、加以設定，以及控制工作執行、叢集管理和啟動動作的細微設定。

> [AZURE.NOTE] 目前僅支援 Azure HDInsight 叢集的隨選組態。

## Azure HDInsight 隨選連結服務
Azure Data Factory 服務可自動建立以 Windows/Linux 為基礎的隨選 HDInsight 叢集來處理資料。此叢集會建立在與叢集相關聯的儲存體帳戶 (JSON 中的 linkedServiceName 屬性) 相同的區域中。

請注意下列有關隨選 HDInsight 連結服務的**重點**：

- 您不會看到隨選 HDInsight 叢集建立在您的 Azure 訂用帳戶中；Azure Data Factory 服務會代表您管理隨選 HDInsight 叢集。
- 在隨選 HDInsight 叢集上執行之工作的記錄檔會被複製到與 HDInsight 叢集相關聯的儲存體帳戶。您可以從 Azure 入口網站的 [**活動執行詳細資料**] 刀鋒視窗存取這些記錄檔。如需詳細資訊，請參閱[監視及管理管線](data-factory-monitor-manage-pipelines.md)一文。
- 只會針對 HDInsight 叢集啟動並執行工作的時間來向您收取費用。

> [AZURE.IMPORTANT] 通常會花費 **15 分鐘**以上的時間來佈建隨選 Azure HDInsight 叢集。

### 範例
下列 JSON 會定義以 Linux 為基礎的隨選 HDInsight 連結服務。Data Factory 服務會在處理資料配量時自動建立**以 Linux 為基礎的** HDInsight 叢集。


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

若要使用以 Windows 為基礎的 HDInsight 叢集，請將 **osType** 設為 **windows**，或者不要使用此屬性，因為預設值是︰windows。

> [AZURE.IMPORTANT] 
HDInsight 叢集會在您於 JSON 中指定的 Blob 儲存體 (**linkedServiceName**) 建立**預設容器**。HDInsight 不會在刪除叢集時刪除此容器。原先的設計就是如此。使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (**timeToLive**)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時予以刪除。
> 
> 隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。這些容器的名稱遵循下列模式："adf**yourdatafactoryname**-**linkedservicename**-datetimestamp"。請使用 [Microsoft 儲存體總管](http://storageexplorer.com/)之類的工具，來刪除 Azure Blob 儲存體中的容器。

### 屬性

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為 **HDInsightOnDemand**。 | 是
clusterSize | 叢集中的背景工作/資料節點數。HDInsight 叢集會利用您為此屬性指定的 2 個前端節點以及背景工作節點數目來建立。節點大小為具有 4 個核心的 Standard\_D3，因此 4 個背景工作節點的叢集將會需要 24 個核心 (4*4 用於背景工作節點 + 2*4 用於前端節點)。如需 Standard\_D3 層的詳細資料，請參閱[在 HDInsight 中建立 Linux 型 Hadoop 叢集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 | 是
timetolive | 隨選 HDInsight 叢集允許的閒置時間。指定如果叢集中沒有其他作用中工作，隨選 HDInsight 叢集在活動執行完成後會保持運作的時間長度。<br/><br/>例如，如果活動執行花費 6 分鐘，而 timetolive 設為 5 分鐘，則叢集會在 6 分鐘的活動執行處理後保持運作 5 分鐘。如果另一輪活動執行了 6 分鐘，則會由相同的叢集處理。<br/><br/>建立隨選 HDInsight 叢集是昂貴的作業 (可能需要一些時間)，所以視需要使用此設定，藉由重複使用隨選 HDInsight 叢集來改善 Data Factory 的效能。<br/><br/>如果您將 timetolive 值設為 0，則處理活動執行後便會刪除叢集。另一方面，如果您設定較高的值，叢集可能會有不必要的閒置而導致高成本。因此，請務必根據您的需求設定適當的值。<br/><br/>如果適當地設定 timetolive 屬性值，則多個管線可以共用相同的隨選 HDInsight 叢集執行個體 | 是
版本 | HDInsight 叢集的版本。針對 Windows 叢集的預設值為 3.1，針對 Linux 叢集的預設值為 3.2。 | 否
linkedServiceName | 隨選叢集用於儲存及處理資料的 Blob 存放區。 | 是
additionalLinkedServiceNames | 指定 HDInsight 連結服務的其他儲存體帳戶，讓 Data Factory 服務代表您註冊它們。 | 否
osType | 作業系統的類型。允許的值為：Windows (預設值) 和 linux | 否
hcatalogLinkedServiceName | 指向 HCatalog 資料庫的 Azure SQL 連結服務名稱。將會使用 Azure SQL 資料庫作為中繼存放區，建立隨選 HDInsight 叢集。 | 否


#### additionalLinkedServiceNames JSON 範例

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### 進階屬性

您也可以針對隨選 HDInsight 叢集的細微組態指定下列屬性。

屬性 | 說明 | 必要
:-------- | :----------- | :--------
coreConfiguration | 指定要建立之 HDInsight 叢集的核心組態參數 (如 core-site.xml 所示)。 | 否
hBaseConfiguration | 指定 HDInsight 叢集的 HBase 組態參數 (hbase-site.xml)。 | 否
hdfsConfiguration | 指定 HDInsight 叢集的 HDFS 組態參數 (hdfs-site.xml)。 | 否
hiveConfiguration | 指定 HDInsight 叢集的 hive 組態參數 (hive-site.xml)。 | 否
mapReduceConfiguration | 指定 HDInsight 叢集的 MapReduce 組態參數 (mapred-site.xml)。 | 否
oozieConfiguration | 指定 HDInsight 叢集的 Oozie 組態參數 (oozie-site.xml)。 | 否
stormConfiguration | 指定 HDInsight 叢集的 Storm 組態參數 (storm-site.xml)。 | 否
yarnConfiguration | 指定 HDInsight 叢集的 Yarn 組態參數 (yarn-site.xml)。 | 否

#### 範例 – 包含進階屬性的隨選 HDInsight 叢集組態

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "timeToLive": "01:30:00",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx4000m",
	        "mapreduce.map.java.opts": "-Xmx4000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000",
	        "mapreduce.map.memory.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

### 節點大小
您可使用下列屬性指定前端、資料和的 zookeeper 節點的大小。

屬性 | 說明 | 必要
:-------- | :----------- | :--------
headNodeSize | 指定前端節點的大小。預設值為：Standard\_D3。如需詳細資料，請參閱以下**指定節點大小**一節。 | 否
dataNodeSize | 指定資料節點的大小。預設值為：Standard\_D3。 | 否
zookeeperNodeSize | 指定 Zoo Keeper 節點的大小。預設值為：Standard\_D3。 | 否
 
#### 指定節點大小
如需了解需為上方屬性指定的字串值，請參閱[虛擬機器的大小](../virtual-machines/virtual-machines-linux-sizes.md#size-tables)一文。值必須符合本文件中所參考的 **CMDLET 與 APIS**。如您在文中所見，若資料節點的大小設定為大 (預設值)，則記憶體大小為 7 GB，其可能不適用於您的案例。

若想要建立 D4 大小的前端節點與背景工作節點，必須指定 **Standard\_D4** 作為 headNodeSize 與 dataNodeSize 屬性的值。

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

若您為這些屬性指定錯誤的值，可能會顯示下列**錯誤：**無法建立叢集。例外狀況：無法完成叢集建立作業。作業失敗 (錯誤代碼「400」)。叢集剩餘狀態：「錯誤」。訊息：「PreClusterCreationValidationFailure」。若顯示此錯誤，請確認您使用來自前述文章中資料表的 **CMDLET 與 APIS**名稱。



## 自備計算環境

在這種組態中，使用者可以將現有的運算環境註冊為 Data Factory 中的連結服務。此運算環境是由使用者管理並由 Data Factory 服務用來執行活動。

下列計算環境可支援這類型的組態：

- Azure HDInsight
- Azure Batch
- Azure Machine Learning

## Azure HDInsight 連結服務

您可以建立 Azure HDInsight 連結服務，以向 Data Factory 註冊自己的 HDInsight 叢集。

### 範例

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### 屬性

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為 **HDInsight**。 | 是
clusterUri | HDInsight 叢集的 URI。 | 是
username | 指定要用來連接到現有 HDInsight 叢集的使用者名稱。 | 是
password | 指定使用者帳戶的密碼。 | 是
linkedServiceName | 此 HDInsight 叢集所使用之 Blob 儲存體的連結服務名稱。 | 是

## Azure Batch 連結服務

您可以建立 Azure Batch 連結服務，以向 Data Factory 註冊虛擬機器 (VM) 的 Batch 集區。您可以使用 Azure Batch 或 Azure HDInsight 執行 .NET 自訂活動。

如果您不熟悉 Azure Batch 服務，請參閱下列主題：


- [Azure Batch 基本知識](../batch/batch-technical-overview.md)，以取得 Azure Batch 服務的概觀。
- [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) Cmdlet 可建立 Azure Batch 帳戶 (或) [Azure 入口網站](../batch/batch-account-create-portal.md)，以使用 Azure 入口網站建立 Azure Batch 帳戶。如需使用此 Cmdlet 的詳細指示，請參閱[使用 PowerShell 管理 Azure Batch 帳戶](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) (英文) 主題。
- [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) Cmdlet 可建立 Azure Batch 集區。

### 範例

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

將 "**.<地區名稱**" 附加至為 **accountName** 屬性所用的 Batch 帳戶名稱。範例：

			"accountName": "mybatchaccount.eastus"

另一個選項是提供 batchUri 端點，如下所示。

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

### 屬性

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為 **AzureBatch**。 | 是
accountName | 建立 Azure Batch 帳戶。 | 是
accessKey | Azure Batch 帳戶的存取金鑰。 | 是
poolName | 虛擬機器的集區名稱。 | 是
linkedServiceName | 與此 Azure Batch 連結服務相關聯的 Azure 儲存體服務連結名稱。此連結服務用於執行活動及儲存活動執行記錄檔所需的暫存檔案。 | 是


## Azure Machine Learning 連結服務

您可建立 Azure Machine Learning 連結服務，以向 Data Factory 註冊 Machine Learning 批次評分端點。

### 範例

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### 屬性

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為：**AzureML**。 | 是
mlEndpoint | 批次評分 URL。 | 是
apiKey | 已發佈的工作區模型的 API。 | 是


## Azure 資料湖分析連結服務
您應建立 **Azure 資料湖分析**連結服務，將 Azure 資料湖分析計算服務連結至 Azure Data Factory，然後再使用管線中的[資料湖分析 U-SQL 活動](data-factory-usql-activity.md)。

下列範例提供 Azure 資料湖分析連結服務的 JSON 定義。

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>",
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


下表提供 JSON 定義中所使用之屬性的描述：

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | type 屬性應設為：**AzureDataLakeAnalytics**。 | 是
accountName | Azure 資料湖分析帳戶名稱。 | 是
dataLakeAnalyticsUri | Azure 資料湖分析 URI。 | 否
authorization | 按一下 Data Factory 編輯器中的 [授權] 按鈕並完成 OAuth 登入後，即會自動擷取授權碼。 | 是
subscriptionId | Azure 訂用帳戶識別碼 | 否 (如果未指定，便會使用 Data Factory 的訂用帳戶)。
resourceGroupName | Azure 資源群組名稱 | 否 (若未指定，便會使用 Data Factory 的資源群組)。
sessionId | OAuth 授權工作階段的工作階段識別碼。每個工作階段識別碼都是唯一的，只能使用一次。這是在 Data Factory 編輯器中自動產生。 | 是

您使用 [授權] 按鈕所產生的授權碼在一段時間後會到期。請參閱下表以了解不同類型的使用者帳戶的到期時間。當驗證**權杖到期**時，您可能會看到下列錯誤訊息：認證作業發生錯誤：invalid\_grant - AADSTS70002：驗證認證時發生錯誤。AADSTS70008：提供的存取授權已過期或撤銷。追蹤識別碼：d18629e8-af88-43c5-88e3-d8419eb1fca1 相互關連識別碼：fac30a0c-6be6-4e02-8d69-a776d2ffefd7 時間戳記：2015-12-15 21:09:31Z

| 使用者類型 | 到期時間 |
| :-------- | :----------- | 
| 不受 Azure Active Directory 管理的使用者帳戶 (@hotmail.com、@live.com 等) | 12 小時 |
| 受 Azure Active Directory (AAD) 管理的使用者帳戶 | 最後一次執行配量後的 14 天。<br/><br/>如果以 OAuth 式連結服務為基礎的配量至少每 14 天執行一次，則為 90 天。 |
 
如果要避免/解決此錯誤，您必須在**權杖到期**時使用 [授權] 按鈕重新授權，然後重新部署連結服務。您也可以使用下一節中的程式碼以程式設計方式產生 sessionId 和 authorization 屬性的值。

### 若要以程式設計方式產生 sessionId 與 authorization 的值 
下列程式碼會產生 **sessionId** 與 **authorization** 值。

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

請參閱 [AzureDataLakeStoreLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主題，以取得在程式碼中使用的 Data Factory 類別的詳細資訊。您必須針對 WindowsFormsWebAuthenticationDialog 類別將參考新增至：Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll。
 

## Azure SQL 連結服務

您可建立 Azure SQL 連結服務，並將其與[預存程序活動](data-factory-stored-proc-activity.md)搭配使用，以叫用 Data Factory 管線中的預存程序。如需此連結服務的詳細資料，請參閱 [Azure SQL 連接器](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)一文。

<!---HONumber=AcomDC_0518_2016-->