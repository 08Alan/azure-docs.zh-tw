<properties
	pageTitle="從 SQL Server 來回移動資料 | Azure Data Factory"
	description="了解如何使用 Azure Data Factory，從內部部署或 Azure VM 中的 SQL Server 資料庫來回移動資料。"
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
	ms.date="04/18/2016"
	ms.author="spelluru"/>

# 使用 Azure Data Factory 從 SQL Server 內部部署或 IaaS (Azure VM) 上來回移動資料

本文概述如何在 Azure Data Factory 中使用複製活動，將資料從其他資料存放區移到 SQL Server，以及將資料從其他資料存放區移到 SQL Server。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

## 啟用連線

與裝載於內部部署或 Azure IaaS (基礎結構即為服務) VM 中的 SQL Server 連線所需的概念和步驟都相同。在這兩種情況下，您需要運用資料管理閘道器進行連線。

請參閱[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文來了解資料管理閘道器和設定閘道器的逐步指示。設定閘道器執行個體是與 SQL Server 連線的必要條件。

雖然您可以在與 SQL Server 相同的內部部署電腦或雲端 VM 執行個體上安裝閘道器，仍建議您在個別的電腦或雲端 VM 上安裝，以避免資源爭用。

下列範例顯示如何在 SQL Server 和 Azure Blob 儲存體將資料複製進來和複製出去。不過，您可以在 Azure Data Factory 中使用複製活動，從任何來源**直接**將資料複製到[這裡](data-factory-data-movement-activities.md#supported-data-stores)所說的任何接收器。

## 範例：從 SQL Server 複製資料到 Azure Blob

下列範例顯示：

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將屬於時間序列的資料從 SQL Server 資料庫中的資料表複製到 Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

在第一個步驟中，請根據[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文中的指示，設定資料管理閘道器。

**SQL Server 連結服務**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure Blob 儲存體連結服務**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**SQL Server 輸入資料集**

此範例假設您已在 SQL Server 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。請注意，您可以在使用單一資料集的相同資料庫內，透過多個資料表進行查詢，但是單一資料表必須用於資料集的 tableName typeProperty。

設定 “external”: ”true” 和指定 externalData 原則資訊和 Azure Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **SqlSource**，而 **sink** 類型設為 **BlobSink**。針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}


在上述範例中，已為 SqlSource 指定 **sqlReaderQuery**。複製活動會針對 SQL Server 資料庫來源執行這項查詢以取得資料。或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。請注意，sqlReaderQuery 可以參考輸入資料集所參考資料庫內的多個資料表；不限於僅設定為資料集的 tableName typeProperty 的資料表。


如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 SQL Server 資料庫執行 (從 mytable 選取 column1、column2)。如果資料集定義沒有結構，則會從資料表中選取所有資料行。


如需 SqlSource 和 BlobSink 所支援屬性的清單，請參閱 [SQL 來源](#sqlsource)小節和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

## 範例：從 Azure Blob 複製資料到 SQL Server

下列範例顯示：

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將屬於時間序列的資料從 Azure Blob 複製到 SQL Server 資料庫中的資料表。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**SQL Server 連結服務**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure Blob 儲存體連結服務**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 輸入資料集**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。資料夾路徑使用開始時間的年、月、日部分，而檔案名稱使用開始時間的小時部分。“external”: “true” 設定會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**SQL Server 輸出資料集**

此範例會將資料複製到 SQL Server 中名為 "MyTable" 的資料表。您應該在 SQL Server 中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。此資料表會每小時加入新的資料列。

	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **SqlSink**。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": " SqlServerOutput "
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## SQL Server 連結服務屬性

下表提供 SQL Server 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | 類型屬性應設為：**OnPremisesSqlServer**。 | 是 |
| connectionString | 指定使用 SQL 驗證或 Windows 驗證連接至內部部署 SQL Server 資料庫所需的 connectionString 資訊。 | 是 |
| gatewayName | Data Factory 服務應該用來連接到內部部署 SQL Server 資料庫的閘道器名稱。 | 是 |
| username | 如果您使用「Windows 驗證」，請指定使用者名稱。範例︰**domainname\\username**。 | 否 |
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 | 否 |

您可以使用 **New-AzureRmDataFactoryEncryptValue** Cmdlet 加密認證，並在連接字串中使用這些認證，如下列範例所示 (**EncryptedCredential** 屬性)：

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### 範例

**使用 SQL 驗證的 JSON**

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
			"typeProperties": {
	        	"connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        	"gatewayName": "<gateway name>"
			}
	    }
	}

**使用 Windows 驗證的 JSON**

如果已指定使用者名稱和密碼，閘道器會使用它們來模擬指定的使用者帳戶來連接至內部部署 SQL Server 資料庫。否則閘道器將會直接利用閘道器的安全性內容 (其啟動帳戶) 連接到 SQL Server。

	{
	     "Name": " MyOnPremisesSQLDB",
	     "Properties":
	     {
	         "type": "OnPremisesSqlLinkedService",
			 "typeProperties": {
	         	"ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
	         	"username": "<domain\\username>",
	         	"password": "<password>",
	         	"gatewayName": "<gateway name>"
			}
	     }
	}

如需為 SQL Server 資料來源設定認證的詳細資料，請參閱[設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)。

## SQL Server 資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Server、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。**SqlServerTable** 資料集類型的 **typeProperties** 區段具有下列屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| tableName | SQL Server Database 執行個體中連結服務所參照的資料表名稱。 | 是 |

## SQL Server 複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

> [AZURE.NOTE] 複製活動只會採用一個輸入，而且只產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

### SqlSource

在複製活動的案例中，如果來源的類型為 **SqlSource**，則 **typeProperties** 區段有下列可用屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 使用自訂查詢來讀取資料。 | SQL 查詢字串。例如：select * from MyTable。可以參考輸入資料集所參考資料庫中的多個資料表。如果未指定，執行的 SQL 陳述式：select from MyTable。 | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 | 預存程序的名稱。 | 否 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |

如果已為 SqlSource 指定 **sqlReaderQuery**，複製活動會針對 SQL Server 資料庫來源執行這項查詢以取得資料。

或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。

如果您未指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，就會使用資料集 JSON 的結構區段中定義的資料行來建立一個查詢，以對 SQL Server 資料庫執行 (從 mytable 選取 column1、column2)。如果資料集定義沒有結構，則會從資料表中選取所有資料行。

> [AZURE.NOTE] 當您使用 **sqlReaderStoredProcedureName** 時，仍必須為資料集 JSON 中的 **tableName** 屬性指定值。這是該產品目前的功能限制。雖然目前尚未針對此資料表來進行驗證。

### SqlSink

**SqlSink** 支援下列屬性：


| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。 | (單位 = 時間範圍) 範例：“00:30:00” (30 分鐘)。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中 | 整數。(單位 = 資料列計數) | 否 (預設值 = 10000)
| sqlWriterCleanupScript | 使用者指定了可供複製活動執行的查詢，以便清除特定配量的資料。如需詳細資訊，請參閱下面「重複性」一節。 | 查詢陳述式。 | 否 |
| sliceIdentifierColumnName | 使用者指定了可供複製活動使用自動產生的配量識別碼填入的資料行名稱，在重新執行時將用來清除特定配量的資料。如需詳細資訊，請參閱下面「重複性」一節。 | 資料類型為 binary(32) 之資料行的資料行名稱。 | 否 |
| sqlWriterStoredProcedureName | 將資料更新插入 (更新/插入) 目標資料表中的預存程序名稱。 | 預存程序的名稱。 | 否 |
| storedProcedureParameters | 預存程序的參數。 | 名稱/值組。參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| sqlWriterTableType | 使用者指定了要用於上述預存程序中的資料表類型名稱。複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 | 資料表類型名稱。 | 否 |

## 疑難排解連線問題

1. 將 SQL Server 設定成接受遠端連線。啟動 [SQL Server Management Studio]、用滑鼠右鍵按一下 [伺服器]，然後按一下 [屬性]。選取清單中 [連接]，然後核取 [允許此伺服器的遠端連接]。

	![啟用遠端連線](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	如需詳細步驟，請參閱[設定 remote access 伺服器組態選項](https://msdn.microsoft.com/library/ms191464.aspx)。
2. 啟動 [SQL Server 組態管理員]。展開您想要之執行個體的 [SQL Server 網路組態]，然後選取 [MSSQLSERVER 的通訊協定]。您應該會在右窗格中看到通訊協定。用滑鼠右鍵按一下 [TCP/IP]，然後按一下 [啟用] 來啟用 TCP/TP。

	![啟用 TCP/IP](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	如需啟用 TCP/IP 通訊協定的詳細資料及替代方式，請參閱[啟用或停用伺服器網路通訊協定](https://msdn.microsoft.com/library/ms191294.aspx)。
3. 在相同的視窗中，按兩下 [TCP/IP] 來啟動 [TCP/IP 屬性] 視窗。
4. 切換到 [IP 位址] 索引標籤。向下捲動到 [IPAll] 區段。記下 **TCP 連接埠** (預設值是 **1433**)。
5. 在電腦上建立 **Windows 防火牆規則**，來允許透過此連接埠的連入流量。  
6. **確認連線**：利用 SQL Server Management Studio，來從不同的電腦使用完整名稱連線到 SQL Server。例如：<machine>.<domain>.corp.<company>.com,1433。

	> [AZURE.IMPORTANT]
	如需詳細資訊，請參閱[連接埠和安全性考量](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations)。
	>   
	> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱[閘道器疑難排解](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)。

## 目標資料庫中的身分識別資料行
本節提供的範例將示範，如何把資料從沒有身分識別資料行的來源資料表，複製到具有身分識別資料行的目的地資料表。

**來源資料表：**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**目的地資料表：**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


請注意，目標資料表具有身分識別資料行。

**來源資料集 JSON 定義**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**目的地資料集 JSON 定義**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }
	}


請注意，您的來源資料表與目標資料表的結構描述不同 (目標資料表有一個具有身分識別的額外資料行)。在此案例中，您必須在目標資料集定義中指定 **structure** 屬性，這不包含身分識別資料行。

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### SQL Server 和 Azure SQL 的類型對應

如[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會使用下列 2 個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Azure SQL、SQL Server、Sybase 來回移動資料時，將使用下列從 SQL 類型到 .NET 類型的對應，反之亦然。

此對應與 ADO.NET 的 SQL Server 資料類型對應相同。

| SQL Server Database Engine 類型 | .NET Framework 類型 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | 位元組 |
| bit | Boolean |
| char | String、Char |
| 日期 | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 十進位 | 十進位 |
| FILESTREAM 屬性 (varbinary(max)) | 位元組 |
| Float | 兩倍 |
| image | 位元組 |
| int | Int32 |
| money | 十進位 |
| nchar | String、Char |
| ntext | String、Char |
| numeric | 十進位 |
| nvarchar | String、Char |
| real | 單一 |
| rowversion | 位元組 |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 十進位 |
| sql\_variant | 物件 * |
| 文字 | String、Char |
| 分析 | TimeSpan |
| timestamp | 位元組 |
| tinyint | 位元組 |
| uniqueidentifier | Guid |
| varbinary | 位元組 |
| varchar | String、Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## 效能和微調  
請參閱「[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)」一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

<!---HONumber=AcomDC_0525_2016-->