<properties 
	pageTitle="教學課程：使用 Azure 入口網站建立具有複製活動的管線 | Microsoft Azure" 
	description="在本教學課程中，您會使用 Azure 入口網站中的 Data Factory 編輯器，建立具有複製活動的 Azure Data Factory 管線。" 
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
	ms.topic="get-started-article" 
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# 教學課程：使用 Azure 入口網站建立具有複製活動的管線
> [AZURE.SELECTOR]
- [概觀和必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)


本教學課程示範如何使用 Azure 入口網站建立和監視 Azure Data Factory。Data Factory 中的管線會使用複製活動將資料從 Azure Blob 複製到 Azure SQL Database。

以下是您會在本教學課程中執行的步驟：

步驟 | 說明
-----| -----------
[建立 Azure Data Factory](#create-data-factory) | 在此步驟中，您會建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。  
[建立連結服務](#create-linked-services) | 在此步驟中，您將建立兩個連結服務：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。AzureStorageLinkedService 會連結 Azure 儲存體，而 AzureSqlLinkedService 會將 Azure SQL Database 連結至 ADFTutorialDataFactory。管線的輸入資料位於 Azure Blob 儲存體的 Blob 容器中，輸出資料則儲存在 Azure SQL Database 的資料表中。因此，您可以將這兩個資料存放區以連結服務的形式新增至 Data Factory。      
[建立輸入和輸出資料集](#create-datasets) | 在上一個步驟中，您所建立的連結服務參照含有輸入/輸出資料的資料存放區。在此步驟中，您會定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表儲存在資料存放區的輸入/輸出資料。您會針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。您也會指定其他屬性，例如結構、可用性和原則。 
[建立管線](#create-pipeline) | 在此步驟中，您會在 ADFTutorialDataFactory 中建立名為 **ADFTutorialPipeline** 的管線。管線有一個**複製活動**，會將輸入資料從 Azure Blob 複製到輸出 Azure SQL 資料表。複製活動會在 Azure Data Factory 中執行資料移動。此活動是由全域可用的服務所提供，可以使用安全、可靠及可調整的方式，在各種不同的資料存放區之間複製資料。如需複製活動的詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。 
[監視管線](#monitor-pipeline) | 在此步驟中，您會使用 Azure 入口網站來監視輸入和輸出資料表的配量。

> [AZURE.IMPORTANT] 
請檢閱[教學課程概觀](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)文章，並在執行本教學課程之前完成**必要**步驟。

## 建立 Data Factory
在此步驟中，您會使用 Azure 入口網站來建立名為 **ADFTutorialDataFactory** 的 Azure Data Factory。

1.	登入 [Azure 入口網站][azure-portal]之後，請按一下左下角的 [新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。

	![新增->DataFactory][image-data-factory-new-datafactory-menu]

6. 在 [新增 Data Factory] 刀鋒視窗中：
	1. 輸入 **ADFTutorialDataFactory** 做為名稱。
	
  		![新增 Data Factory 刀鋒視窗][image-data-factory-getstarted-new-data-factory-blade]
	2. 按一下 [資源群組名稱]，然後執行下列步驟：
		1. 按一下 [建立新的資源群組]。
		2. 在 [建立資源群組] 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為資源群組的名稱，然後按一下 [確定]。

			![建立資源群組][image-data-factory-create-resource-group]

		本教學課程的某些步驟是假設您使用 **ADFTutorialResourceGroup** 做為資源群組名稱。若要了解資源群組，請參閱[使用資源群組管理您的 Azure 資源](../resource-group-overview.md)。
7. 在 [新增 Data Factory] 刀鋒視窗中，請留意是否已選取 [新增至開始面板]。
8. 按一下 [新增 Data Factory] 刀鋒視窗中的 [建立]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤，請變更 Data Factory 名稱 (例如 yournameADFTutorialDataFactory)，然後試著重新建立。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
	
		Data factory name “ADFTutorialDataFactory” is not available  
	 
	![Data Factory 名稱無法使用][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。
	> 
	> 若要建立 Data Factory 執行個體，您必須是 Azure 訂用帳戶的參與者/系統管理員

9. 按一下左側的 [通知] 中樞，然後從建立程序中尋找通知。按一下 **X** 關閉 [通知] 刀鋒視窗 (若已開啟)。
10. 建立完成之後，您會看到如圖所示的 [DATA FACTORY] 刀鋒視窗。

    ![Data Factory 首頁][image-data-factory-get-stated-factory-home-page]

## 建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結服務：**AzureStorageLinkedService** 和 **AzureSqlLinkedService**。AzureStorageLinkedService 連結服務會連結 Azure 儲存體帳戶，AzureSqlLinkedService 則會將 Azure SQL Database 連結至 **ADFTutorialDataFactory**。您稍後會在本教學課程中建立管線，以將資料從 AzureStorageLinkedService 中的 Blob 容器複製到 AzureSqlLinkedService 中的 SQL 資料表。

### 建立 Azure 儲存體帳戶的連結服務
1.	在 [DATA FACTORY] 刀鋒視窗中，按一下 [製作和部署] 磚來啟動 Data Factory 的 [編輯器]。

	![[製作和部署] 磚][image-author-deploy-tile]

	 
5. 在 [編輯器] 中，按一下工具列上的 [新增資料存放區] 按鈕，然後從下拉式功能表中選取 [Azure 儲存體]。在右窗格中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 範本。

	![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 將 **accountname** 和 **accountkey** 取代為 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰值。

	![編輯器 Blob 儲存體 JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

6. 按一下工具列上的 [部署]，部署 AzureStorageLinkedService。確認您在標題列看到 [已成功建立連結服務] 訊息。

	![編輯器 Blob 儲存體部署][image-editor-blob-storage-deploy]

### 建立 Azure SQL Database 的連結服務
1. 在 [Data Factory 編輯器] 中，按一下工具列上的 [新增資料存放區] 按鈕，然後從下拉式功能表中選取 [Azure SQL Database]。在右窗格中，您應該會看到用來建立 Azure SQL 連結服務的 JSON 範本。

	![編輯器 Azure SQL 設定][image-editor-azure-sql-settings]

2. 將 **servername**、**databasename**、**username@servername** 和 **password** 替換為您的 Azure SQL 伺服器名稱、資料庫名稱、使用者帳戶和密碼。
3. 按一下工具列上的 [**部署**]，建立並部署 AzureSqlLinkedService。
   

## 建立資料集
在上一個步驟中，您已建立連結服務 **AzureStorageLinkedService** 和 **AzureSqlLinkedService**，將 Azure 儲存體帳戶和 Azure SQL Database 連結至 Data Factory：**ADFTutorialDataFactory**。在此步驟中，您會定義兩個 Data Factory 資料表：**EmpTableFromBlob** 和 **EmpSQLTable**，它們分別代表 AzureStorageLinkedService 和 AzureSqlLinkedService 所參照資料存放區中所儲存的輸入/輸出資料。您會針對 EmpTableFromBlob，指定所含 Blob 具有來源資料的 Blob 容器，而針對 EmpSQLTable 指定可儲存輸出資料的 SQL 資料表。

### 建立輸入資料集 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您會在 **AzureStorageLinkedService** 連結服務所代表的 Azure 儲存體中，建立指向 Blob 容器的 **EmpBlobTable** 資料表。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增資料集] 按鈕，然後按下拉式功能表中的 [Blob 資料表]。
2. 將右窗格中的 JSON 替換為以下 JSON 片段：

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     請注意下列幾點：
	
	- 資料集 **type** 設為 **AzureBlob**。
	- **linkedServiceName** 設為 **AzureStorageLinkedService**。您已在步驟 2 中建立此連結服務。
	- **folderPath** 設為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。
	- 格式 **type** 設為 **TextFormat**
	- 文字檔中有兩個欄位 (**FirstName** 和 **LastName**)，以逗號字元分隔 (**columnDelimiter**)
	- **availability** 設定為**每小時** (**frequency** 設定為**小時**，**interval** 設定為 **1**)。因此，Data Factory 會每小時都在您指定之 Blob 容器 (**adftutorial**) 的根資料夾中尋找輸入資料。
	

	如果您沒有指定**輸入** **資料表** 的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。
 
	如果您未指定輸出資料表的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：Data.&lt;Guid&gt;.txt (範例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

	若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，folderPath 使用 SliceStart (所處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

2. 按一下工具列上的 [部署]，建立和部署 **EmpTableFromBlob** 資料表。確認您在編輯器的標題列看到 [已成功建立資料表] 訊息。

### 建立輸出資料集
在此步驟的這個部分中，您會建立名為 **EmpSQLTable** 的輸出資料集。此資料集指向 Azure SQL Database 中 **AzureSqlLinkedService** 所代表的 SQL 資料表。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增資料集] 按鈕，然後按下拉式功能表中的 [Azure SQL 資料表]。
2. 將右窗格中的 JSON 替換為以下 JSON 片段：

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     請注意下列幾點：
	
	* 資料集 **type** 設為 **AzureSQLTable**。
	* **linkedServiceName** 設為 **AzureSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	* **tablename** 設為 **emp**。
	* 資料庫的 emp 資料表中有三個資料行 – **ID**、**FirstName** 和 **LastName**。ID 是識別資料行，所以您只需在此指定 **FirstName** 和 **LastName**。
	* **availability** 設定為**每小時**，且 (**frequency** 設定為**小時**，**interval** 設定為 **1**)。Data Factory 服務會每隔一小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。


3. 按一下工具列上的 [部署]，建立並部署 **EmpSQLTable** 資料表。


## 建立管線
在此步驟中，您會建立管線，其中含有使用 **EmpTableFromBlob** 做為輸入和使用 **EmpSQLTable** 做為輸出的複製活動。

1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增管線] 按鈕。如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。或者，您也可以在樹狀檢視中，以滑鼠右鍵按一下 [**管線**]，再按一下 [**新增管線**]。

	![編輯器 [新增管線] 按鈕][image-editor-newpipeline-button]
 
2. 將右窗格中的 JSON 替換為以下 JSON 片段：
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	請注意下列幾點：

	- 在活動區段中，只會有一個活動的 **type** 設為 **CopyActivity**。
	- 活動的輸入設為 **EmpTableFromBlob**，活動的輸出則設為 **EmpSQLTable**。
	- 在 **transformation** 區段中，來源類型指定為 **BlobSource**，接收類型指定為 **SqlSink**。

	將 **start** 屬性的值替換為目前日期，並將 **end** 值替換為隔天的日期。在日期時間中，您只指定日期部分，並略過時間部分。例如，"2015-02-03"，這相當於 "2015-02-03T00:00:00Z"
	
	開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

4. 按一下工具列上的 [部署]，建立並部署 **ADFTutorialPipeline**。確認您看到 [已成功建立管線] 訊息。
5. 現在，按一下 **X** 關閉 [編輯器] 刀鋒視窗。再按一次 **X**，關閉內含工具列和樹狀檢視的 [ADFTutorialDataFactory] 刀鋒視窗。如果您看到 [將捨棄未儲存的編輯] 訊息，請按一下 [確定]。
6. 您應該會回到 **ADFTutorialDataFactory** 的 [DATA FACTORY] 刀鋒視窗。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。
 
### 在圖表檢視中檢視 Data Factory 
1. 在 [DATA FACTORY] 刀鋒視窗中，按一下 [圖表]。

	![Data Factory 刀鋒視窗：圖表磚][image-datafactoryblade-diagramtile]

2. 您應該會看到如下圖所示的圖表：

	![圖表檢視][image-data-factory-get-started-diagram-blade]

	您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。
3. 在圖表檢視中的 **ADFTutorialPipeline** 上按一下滑鼠右鍵，然後按一下 [開啟管線]。您應該會看到管線中的活動，以及活動的輸入和輸出資料集。在本教學課程中，管線中只能有一個活動 (複製活動) 將 EmpTableBlob 做為輸入資料集，並將 EmpSQLTable 做為輸出資料集。

	![開啟管線](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. 按一下左上角階層連結中的 [Data Factory]，回到圖表檢視。圖表檢視會顯示所有管線。在此範例中，您只建立了一個管線。
 

## 監視管線
在此步驟中，您會使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。

1. 如果尚未開啟 [Azure 入口網站 (預覽)][azure-portal]，請瀏覽至該網站。
2. 如果 **ADFTutorialDataFactory** 的刀鋒視窗未開啟，請按一下 [開始面板] 上的 [ADFTutorialDataFactory] 予以開啟。
3. 您應該會看到您在此分頁上建立之資料表和管線的計數和名稱。

	![含名稱的首頁][image-data-factory-get-started-home-page-pipeline-tables]

4. 現在，按一下 [資料集] 磚。
5. 在 [資料集] 刀鋒視窗中，按一下 [EmpTableFromBlob]。此資料集是 **ADFTutorialPipeline** 的輸入資料集。

	![已選取 EmpTableFromBlob 的資料集][image-data-factory-get-started-datasets-emptable-selected]
5. 請注意，截至目前為止的資料配量都已產生，並已「就緒」，因為 **emp.txt** 檔案一直都存在於 Blob 容器中：**adftutorial\\input**。確認下方的 [最近失敗的配量] 區段中沒有任何配量。

	[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更：
    
	按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。
	
	若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。

	![資料配量 (依配量時間)][DataSlicesBySliceTime]

6. 現在，在 [資料集] 刀鋒視窗中，按一下 [EmpSQLTable]。此資料集是 **ADFTutorialPipeline** 的輸出資料集。

	![資料集刀鋒視窗][image-data-factory-get-started-datasets-blade]

6. 您應會看到如下圖所示的 **EmpSQLTable** 刀鋒視窗：

	![資料表刀鋒視窗][image-data-factory-get-started-table-blade]
 
7. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [就緒]。底部的 [問題配量] 區段中沒有顯示任何配量。
8. 按一下 **…** (省略符號) 檢視所有配量。

	![資料配量刀鋒視窗][image-data-factory-get-started-dataslices-blade]

9. 按一下清單中的任何資料配量，您應該會看到 [資料配量] 刀鋒視窗。

	![資料配量刀鋒視窗][image-data-factory-get-started-dataslice-blade]
  
	如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

11. 在 [資料配量] 刀鋒視窗中，您應該會看到底部清單中的所有活動執行。按一下 [活動執行] 查看 [活動執行詳細資料] 刀鋒視窗。

	![活動執行詳細資料][image-data-factory-get-started-activity-run-details]

	
12. 按一下 **X** 關閉所有刀鋒視窗，直到您回到 **ADFTutorialDataFactory** 的起始刀鋒視窗。
14. (選擇性) 在 **ADFTutorialDataFactory** 的起始刀鋒視窗上按一下 [**管線**]，再按一下 [**管線**] 刀鋒視窗中的 **ADFTutorialPipeline**，然後深入檢視輸入資料表 (**已取用**) 或輸出資料表 (**已產生**)。
15. 啟動 **SQL Server Management Studio**，並連接到 Azure SQL Database，然後確認資料列已插入資料庫的 **emp** 資料表中。

	![SQL 查詢結果][image-data-factory-get-started-sql-query-results]


## 摘要 
在本教學課程中，您已建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。您已使用 Azure 入口網站建立 Data Factory、連結服務、資料集和管線。以下是您在本教學課程中執行的高階步驟：

1.	建立 Azure **Data Factory**。
2.	建立**連結服務**：
	1. **Azure 儲存體**連結服務可連結保留輸入資料的 Azure 儲存體帳戶。
	2. **Azure SQL** 連結服務可連結保留輸出資料的 Azure SQL Database。
3.	建立可描述管線輸入資料和輸出資料的**資料集**。
4.	建立具有**複製活動**的**管線**，以 **BlobSource** 做為來源並以 **SqlSink** 做為接收器。


## 另請參閱
| 主題 | 說明 |
| :---- | :---- |
| [資料移動活動](data-factory-data-movement-activities.md) | 本文提供您在本教學課程中使用的複製活動詳細資訊。 |
| [排程和執行](data-factory-scheduling-and-execution.md) | 本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
| [管線](data-factory-create-pipelines.md) | 本文協助您了解 Azure Data Factory 中的管線和活動。 |
| [資料集](data-factory-create-datasets.md) | 本文協助您了解 Azure Data Factory 中的資料集。
| [使用監視應用程式來監視和管理管線](data-factory-monitor-manage-app.md) | 本文說明如何使用監視及管理應用程式，來監視、管理管線及進行偵錯。 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0921_2016-->