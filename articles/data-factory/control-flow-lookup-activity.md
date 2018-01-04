---
title: "Azure Data Factory 中的查閱活動 | Microsoft Docs"
description: "了解如何使用查閱活動從外部來源查閱值。 此輸出可供後續活動進一步參考。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: spelluru
ms.openlocfilehash: f287b0287ad85ffe1654e0d574cd44aa4dd81a0f
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory 中的查閱活動
查閱活動可用來讀取或查閱任何外部來源的記錄/表格名稱/值。 此輸出可供後續活動進一步參考。 

當您想從組態檔或資料來源動態擷取檔案/記錄/表格的清單時，查閱活動就很有用。 活動的輸出可進一步供其他活動使用，以對那些項目執行特定處理。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版本 (GA))，請參閱 [Data Factory V1 文件](v1/data-factory-introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

針對查閱，目前支援下列資料來源：
- Azure Blob 中的 JSON 檔案
- 檔案系統中的 JSON 檔案
- Azure SQL Database (從查詢轉換的 JSON 資料)
- Azure SQL 資料倉儲 （從查詢轉換 JSON 資料）
- SQL Server （從查詢轉換 JSON 資料）
- Azure 表格儲存體 (從查詢轉換的 JSON 資料)

## <a name="syntax"></a>語法

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>類型屬性
名稱 | 說明 | 類型 | 必要
---- | ----------- | ---- | --------
資料集 | 資料集屬性提供查閱的資料集參考。 目前支援的資料集類型為：<ul><li>`AzureBlobDataset`如[Azure Blob 儲存體](connector-azure-blob-storage.md#dataset-properties)做為來源</li><li>`FileShareDataset`如[檔案系統](connector-file-system.md#dataset-properties)做為來源</li><li>`AzureSqlTableDataset`如[Azure SQL Database](connector-azure-sql-database.md#dataset-properties)或[Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#dataset-properties)做為來源</li><li>`SqlServerTable`如[SQL Server](connector-sql-server.md#dataset-properties)做為來源</li><li>`AzureTableDataset`如[Azure 資料表儲存體](connector-azure-table-storage.md#dataset-properties)做為來源</li> | 索引鍵/值組 | 是
來源 | 特定資料集的來源屬性，複製活動來源相同。 了解詳細資料，從每個對應的連接器文件中的 「 複製活動屬性 」 一節。 | 索引鍵/值組 | 是
firstRowOnly | 指出是否要傳回的第一個資料列或所有資料列。 | 布林值 | 編號 預設值為 `ture`。

## <a name="use-lookup-activity-result-in-subsequent-activity"></a>在後續的活動中使用查閱活動結果

查詢結果中傳回`output`> 一節中活動執行結果。

**當`firstRowOnly`設`true`（預設值）**，輸出格式如下所示。 查閱結果低於固定`firstRow`索引鍵。 若要在後續的活動中使用結果，使用的模式`@{activity('MyLookupActivity').output.firstRow.TableName}`。

```json
{
    "firstRow":
    {
        "Id": "1",
        "TableName" : "Table1"
    }
}
```

**當`firstRowOnly`設`false`** ，輸出格式如下所示。 A`count`欄位指出傳回的多少筆記錄，以及詳細的值在固定`value`陣列。 在這種情況下，「 查閱 」 活動通常會接著[Foreach 活動](control-flow-for-each-activity.md)，您可以傳遞`value`ForEach 活動陣列`items`欄位使用的模式`@activity('MyLookupActivity').output.value`。 中的項目存取`value`，使用下列語法： `@{activity('lookupActivity').output.value[zero based index].propertyname}`。 範例如下：`@{activity('lookupActivity').output.value[0].tablename}`

```json
{
    "count": "2",
    "value": [
        {
            "Id": "1",
            "TableName" : "Table1"
        },
        {
            "Id": "2",
            "TableName" : "Table2"
        }
    ]
} 
```

## <a name="example"></a>範例
在此範例中，複製活動會將資料複製 Azure SQL database 中的 SQL 資料表中，Azure Blob 儲存體。 SQL 資料表的名稱會儲存在 Blob 儲存體的 JSON 檔案中。 查閱活動查閱資料表名稱，在執行階段。 這個方法可動態修改 JSON 來，而不必重新部署管線/資料集。 

此範例將示範第一個資料列的查詢。 所有資料列和具有 ForEach 活動鏈結的查詢，請參閱[教學課程-大量複製資料](tutorial-bulk-copy.md)範例。

### <a name="pipeline"></a>管線
這個管線包含兩個活動：**查閱**和**複製**。 

- 查閱活動會設定為使用 LookupDataset，它會參考 Azure Blob 儲存體中的位置。 查閱活動會從這個位置的 JSON 檔案讀取 SQL 資料表的名稱。 
- 複製活動會使用查閱活動 (SQL 資料表名稱) 的輸出。 來源資料集 (SourceDataset) 中的 tableName 會設定為使用查閱活動的輸出。 複製活動會將資料從 SQL 資料表複製到 Azure Blob 儲存體 SinkDataset 所指定的位置。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>查閱資料集
查詢資料集是 AzureStorageLinkedService 所指定 Azure 儲存體中查閱資料夾內的 sourcetable.json 檔案。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>複製活動的來源資料集
來源資料集會使用查閱活動的輸出，亦即 SQL 資料表的名稱。 複製活動會將資料從這個 SQL 資料表複製到接收資料集指定之 Azure Blob 儲存體中的位置。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>複製活動的接收資料集
複製活動會將資料從 SQL 資料表複製到 AzureStorageLinkedService 所指定 Azure 儲存體中的 CSV 資料夾內的 filebylookup.csv 檔案。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
這個儲存體帳戶包含具有 SQL 資料表名稱的 JSON 檔案。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 的連結服務
此 Azure SQL 資料庫包含要複製到 Blob 儲存體的資料。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>物件集合

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>物件的陣列

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [Web 活動](control-flow-web-activity.md)
