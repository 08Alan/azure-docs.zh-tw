---
title: 適用於 Azure Cosmos DB 的 Node.js 範例 | Microsoft Docs
description: 在 GitHub 上找到適用於 Azure Cosmos DB 中一般工作 (包括 CRUD 作業) 的 Node.js 範例。
keywords: Node.js 範例
services: cosmos-db
author: moderakh
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: sample
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: a4bda45f9a620d70c78b7b4142cd5f152bd5436d
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960384"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Azure Cosmos DB Node.js 範例
> [!div class="op_single_selector"]
> * [.NET 範例](sql-api-dotnet-samples.md)
> * [Java 範例](sql-api-java-samples.md)
> * [非同步 Java 範例](sql-api-async-java-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的範例解決方案。 本文提供：

* 每個 Node.js 範例專案檔中各項工作的連結。
* 相關 API 參考內容的連結。

**先決條件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 您可以 [啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

您也需要 [Node.js SDK](sql-api-sdk-node.md)。
   
   > [!NOTE]
   > 每個範例都各自獨立，自己設定，並自行清理。 據此，這些範例對 [DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-
) 發出多個呼叫。 每當執行此動作時，即會根據所建立之集合的效能層，對您的訂用帳戶計入一小時的使用量費用。
   > 
   > 

## <a name="database-examples"></a>資料庫範例
[DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdatabase-body--options--callback-) |
| [查詢帳戶中的資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydatabases-query--options-) |
| [依識別碼讀取資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabase-databaselink--options--callback-) |
| [列出帳戶的資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdatabases-options-) |
| [刪除資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedatabase-databaselink--options--callback-) |

## <a name="collection-examples"></a>集合範例
[CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [讀取資料庫中所有集合的清單](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollections-databaselink--options-) |
| [依 _self 取得集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [依識別碼取得集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readcollection-collectionlink--options--callback-) |
| [取得集合的效能層](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#queryoffers-query--options-) |
| [變更集合的效能層](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replaceoffer-offerlink--offer--callback-) |
| [刪除集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletecollection-collectionlink--options--callback-) |

## <a name="document-examples"></a>文件範例
[DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createdocument-documentsfeedordatabaselink--body--options--callback-) |
| [讀取集合的文件摘要](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [依識別碼讀取文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-) |
| [僅在文件變更時才讀取文件](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#readdocument-documentlink--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [查詢文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#querydocuments-documentsfeedordatabaselink--query--options-) |
| [取代文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-) |
| [以條件式 ETag 檢查取代文件](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacedocument-documentlink--document--options--callback-)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.accesscondition?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_AccessCondition) |
| [刪除文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#deletedocument-documentlink--options--callback-) |

## <a name="indexing-examples"></a>索引範例
[IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [使用預設索引建立集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](https://docs.microsoft.com/en-us/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-) |
| [手動編製特定文件的索引](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [手動從索引中排除特定文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective?view=azure-dotnet#Microsoft_Azure_Documents_Client_RequestOptions_IndexingDirective) |
| [為大量匯入使用延遲索引或讀取重度集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.indexingmode?view=azure-dotnet) |
| [在索引中包含文件的特定路徑](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_IncludedPaths) |
| [在索引中排除特定路徑](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths?view=azure-dotnet#Microsoft_Azure_Documents_IndexingPolicy_ExcludedPaths) |
| [允許在範圍作業期間掃描字串路徑](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery?view=azure-dotnet#Microsoft_Azure_Documents_Client_FeedOptions_EnableScanInQuery) |
| [建立字串路徑的範圍索引](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexkind?view=azure-dotnet#Microsoft_Azure_Documents_IndexKind_Range)、[IndexingPolicy](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.indexingpolicy?view=azure-dotnet)、[DocumentClient.queryDocument](https://docs.microsoft.com/en-us/javascript/api/documentdb/documentclient?view=azure-node-latest) |
| [使用預設 indexPolicy 建立集合，然後在線上加以更新](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createcollection-databaselink--body--options--callback-)<br> [DocumentClient.replaceCollection#replaceCollection](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#replacecollection-collectionlink--collection--options--callback-) |

如需索引編製的詳細資訊，請參閱 [Azure Cosmos DB 編製索引原則](indexing-policies.md)。

## <a name="server-side-programming-examples"></a>伺服器端程式設計範例
[ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [建立預存程序](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#createstoredprocedure-collectionlink--sproc--options--callback-) |
| [執行預存程序](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](https://docs.microsoft.com/javascript/api/documentdb/DocumentClient?view=azure-node-latest#executestoredprocedure-sproclink--params--options--callback-) |

如需伺服器端程式設計的詳細資訊，請參閱 [Azure Cosmos DB 伺服器端程式設計：預存程序、資料庫觸發程序和 UDF](programming.md)。

## <a name="partitioning-examples"></a>資料分割範例
[Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) 專案的 [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) 檔案說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [使用 HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](https://docs.microsoft.com/javascript/api/documentdb/HashPartitionResolver?view=azure-node-latest) |

如需在 Azure Cosmos DB 中分割資料的詳細資訊，請參閱[在 Azure Cosmos DB 中進行資料分割和調整資料](partition-data.md)。

