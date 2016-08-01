<properties
	pageTitle="Azure SQL Database 資源限制"
	description="此頁面描述一些 Azure SQL Database 常見的資源限制。"
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="07/19/2016"
	ms.author="carlrab" />


# Azure SQL Database 資源限制

## 概觀

Azure SQL Database 使用兩種不同機制來管理資料庫可使用的資源：**資源管理**和**限制強制執行**。本主題將說明資源管理的這兩種主要方法。

## 資源管理
基本、標準和進階服務層的設計目的之一，是讓 Azure SQL Database 彷彿在獨立的電腦上執行，與其他資料庫完全隔離。資源管理便會模擬這個行為。如果彙總的資源使用率達到可用 CPU、記憶體、記錄 I/O 和資料 I/O 資源 (指派至資料庫) 的上限，資源管理會將查詢排入執行佇列中，並在系統釋出資源時，適時將資源指派給佇列中的查詢。

在專用的電腦上使用所有可用的資源，會導致目前正在執行的查詢花費更多執行時間，而這可能會使得用戶端上的命令逾時。具備積極重試邏輯的應用程式，以及時常對資料庫執行查詢的應用程式，若在嘗試執行新查詢時達到並行要求的上限，便可能出現錯誤訊息。

### 建議：
資料庫使用率接近上限時，監視資源使用率以及查詢的平均回應時間。查詢的延遲較高時，您通常可採取三種應對方式：

1.	減少資料庫的傳入要求數量，以避免逾時和要求不斷累積。

2.	為資料庫指派更高的效能層級。

3.	將查詢最佳化，以降低每個查詢的資源使用率。如需詳細資訊，請參閱＜Azure SQL Database 效能指南＞一文的「查詢微調/提示」章節。

## 限制強制執行
系統會在達到上限時拒絕新的要求，藉此強制執行 CPU、記憶體、記錄 I/O 和資料 I/O 以外的資源。用戶端收到的[錯誤訊息](sql-database-develop-error-messages.md)會因達到的上限而有所不同。

例如，SQL Database 的連接數，以及可以處理的並行要求數目都將受到限制。SQL Database 可允許資料庫的連接數大於並行要求的數目，以支援連接共用。雖然應用程式能輕易控制可用的連接數，但平行要求的數量通常難以預估及控制。尤其在尖峰負載期間，應用程式往往傳送過多的要求，或是資料庫在達到資源上限後，因為執行耗時較長的查詢而開始累積背景工作執行緒，此時就可能發生錯誤。

## 服務層和效能層級

對於單一資料庫，資料庫的限制是由服務層級和效能等級所定義。下表說明基本、標準和高階資料庫在不同效能等級的特性。

[AZURE.INCLUDE [SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table.md)]

[彈性資料庫集區](sql-database-elastic-pool.md)在集區中的資料庫之間共用資源。下表說明基本、標準和高階彈性資料庫集區的特性。

[AZURE.INCLUDE [彈性資料庫的 SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

如需上述表格所列之每項資源的擴充定義，請參閱[服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)中的說明。如需服務層的概觀，請參閱 [Azure SQL Database 服務層和效能等級](sql-database-service-tiers.md)。

## 其他 SQL Database 限制

| 領域 | 限制 | 說明 |
|---|---|---|
| 每個訂用帳戶使用自動匯出的資料庫 | 10 | 自動匯出可讓您建立自訂排程以備份 SQL Database。如需詳細資訊，請參閱 [SQL Database：自動 SQL Database 匯出的支援](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines)。|
| 每一部伺服器的資料庫 | 最多 5000 個 | 在 V12 伺服器上，每一伺服器最多允許 5000 個資料庫。 |  
| 每一部伺服器的 DTU | 45000 | 在 V12 伺服器上，每一部伺服器會有 45000 個 DTU，用於佈建資料庫、彈性集區和資料倉儲。 |



## 資源

[Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

[Azure SQL Database 服務層和效能層級](sql-database-service-tiers.md)

[SQL Database 用戶端程式的錯誤訊息](sql-database-develop-error-messages.md)

<!---HONumber=AcomDC_0720_2016-->