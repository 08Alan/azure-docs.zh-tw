<properties
   pageTitle="SQL 資料倉儲預覽期望 |Microsoft Azure"
   description="公用預覽版功能和我們的 SQL 資料倉儲公開上市目標的摘要。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/12/2016"
   ms.author="mausher;barbkess;sonyama;nicw"/>

# SQL 資料倉儲預覽版期望

本文將說明 SQL 資料倉儲預覽版功能，以及我們的服務公開上市 (GA) 目標。我們會在增強公用預覽版功能時持續更新這項資訊。

我們的 SQL 資料倉儲目標：

- 可預測的效能以及高達數 PB 資料的線性延展性。
- 所有資料倉儲作業的高度可靠性 (由服務等級協定 (SLA) 支援)。

我們將朝這些目標持續努力，在 SQL 資料倉儲公開上市之前達成目標。

## 可預測且可調整的效能

Azure SQL 資料倉儲引進了資料倉儲單位 (DWU)，做為測量資料倉儲可用計算資源 (CPU、記憶體、儲存體 I/O) 的方法。增加 DWU 數目可增加資源。當 DWU 數目增加時，SQL 資料倉儲作業可跨越更多分散式資源平行執行 (例如資料載入或查詢)。這可減少延遲並改善效能。

任何資料倉儲都具有 2 個基本效能度量：

- 載入速率。每秒可載入資料倉儲的記錄數量。我們會特別測量可透過 PolyBase 從 Azure Blob 儲存體匯入資料表 (具有叢集資料行存放區索引) 的記錄數量。
- 掃描速率。每秒可從資料倉儲循序擷取的記錄數量。我們會特別測量查詢叢集資料行存放區索引所傳回的記錄數量。


我們正在測量一些重要的效能增強功能，而且很快就會分享預期的速率。在預覽期間，我們將持續增強服務品質 (例如提高壓縮和快取) 以提高這些速率，並確保這些速率依可預期的方式調整。


## SLA 所支援的高度可靠性

### 資料保護

SQL 資料倉儲會使用異地備援 blob，將所有資料儲存在在 Azure 儲存體中。並在本機的 Azure 區域維護三份資料的同步複本，確保當地語系化失敗 (例如儲存體磁碟機失敗) 時能夠提供透明的資料保護。此外，還會在遠端 Azure 區域維護三份非同步複本，確保區域失敗 (災害復原) 時能夠提供資料保護。將本機和遠端區域配對，以維持可接受的同步處理延遲 (例如美國東部和西部)。


### 備份

Azure SQL 資料倉儲會使用 Azure 儲存體快照，至少每 8 小時備份一次所有資料。這些快照會保留 7 天。這樣一來，在擷取最新快照時的過去 7 天內，就有至少 21 個時間點可用來還原資料。您可以使用 PowerShell 或 REST API 從快照還原資料。

快照會以非同步的方式複製到遠端 Azure 區域，以在區域失敗 (災害復原) 時增加復原能力。


### 查詢完成

SQL 資料倉儲會將資料儲存於一或多個計算節點，而每個節點可儲存某些使用者資料以及控制該資料的查詢執行。在大量平行處理 (MPP) 架構中，查詢會以平行方式跨計算節點執行。SQL 資料倉儲會自動偵測並降低計算節點失敗。不過，在預覽期間，作業 (例如資料載入或查詢) 可能會因為個別節點失敗而失敗。在預覽期間，我們將持續增強功能，儘管節點失敗能可順利完成作業。


## 後續步驟

[開始使用][]公用預覽版。

<!--Image references-->

<!--Article references-->
[開始使用]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->