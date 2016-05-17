<properties
   pageTitle="什麼是 Azure SQL 資料倉儲 | Microsoft Azure"
   description="企業級分散式資料庫，可處理資料量高達 PB 的關聯式與非關聯式資料。它是業界首見能在幾秒內增加、縮減和暫停的雲端資料倉儲。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/10/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# 什麼是 Azure SQL 資料倉儲？

Azure SQL 資料倉儲是一種雲端架構、相應放大的資料庫，可處理大量的關聯式與非關聯式資料。SQL 資料倉儲是以我們的巨量平行處理 (MPP) 架構為基礎，可以處理您的企業工作負載。

SQL 資料倉儲：

- 結合我們已實證的 SQL Server 關聯式資料庫與我們的 Azure 雲端相應放大功能。您可以在數秒內增加、減少、暫停或繼續計算。這可讓您在有需要時相應放大 CPU，以及在非尖峰時間內減少使用量，進而節省成本。
- 運用我們的 Azure 平台。它很容易部署、可順暢地維護，且自動備份可提供完整容錯。 
- 補充 SQL Server 生態系統。您可以使用熟悉的 SQL Server T-SQL 和工具進行開發。

繼續閱讀以深入了解 SQL 資料倉儲的重要功能。

## 最佳化

### 巨量平行處理 (MPP) 架構

SQL 資料倉儲使用 Microsoft 的巨量平行處理 (MPP) 架構，其設計用來執行一些全球最大的內部部署資料倉儲。

目前，我們的 MPP 架構將您的資料分散於 60 個不共用的儲存和處理單元。資料會儲存在異地複寫的備援 Azure 儲存體 Blob 中，並連結至計算節點以便執行查詢。使用這個架構，我們可以採用分治法來執行複雜的 T-SQL 查詢。處理時，控制節點會剖析查詢，然後各個計算節點會平行「佔領」其資料部分。

藉由結合我們的 MPP 架構與 Azure 儲存體功能，SQL 資料倉儲可以︰

- 擴大或縮小獨立計算的儲存體。
- 擴大或縮小計算，而不移動資料。 
- 暫停計算功能，並使資料保持不變。
- 隨時可以恢復計算功能。

下面有此架構的詳細說明。

![SQL 資料倉儲架構][1]


- **控制節點 ︰**控制節點可「控制」系統。它是與所有應用程式與連接互動的前端。在 SQL 資料倉儲中，控制節點採用 SQL Database 計算，而且連接它時的外觀及操作方式相同。控制節點會暗中協調對分散式資料執行平行查詢所需的所有資料移動和計算。當您將 TSQL 查詢提交到 SQL 資料倉儲時，控制節點會將其轉換為會在每個計算節點上平行執行的個別查詢。

- **計算節點︰**計算節點是 SQL 資料倉儲背後的動力。它們是處理查詢步驟及管理您的資料的 SQL Database。當您新增資料時，SQL 資料倉儲會使用您的計算節點來分散資料列。計算節點也是會對您的資料執行平行查詢的背景工作。經過處理後，它們會將結果傳回控制節點。為了完成查詢，控制節點會彙總結果並傳回最終的結果。


- **儲存體：**您的資料會儲存在 Azure 儲存體 Blob 中。當計算節點與您的資料互動時，它們會直接從 Blob 儲存體來回寫入和讀取。由於 Azure 儲存體可不受限地明確擴充，SQL 資料倉儲同樣也可以。計算和儲存體各自獨立，所以 SQL 資料倉儲可以自動調整儲存體，與計算分開調整，反之亦然。Azure 儲存體也是完全容錯並可簡化備份和還原程序。
   

- **資料移動服務︰**資料移動服務 (DMS) 是我們在節點之間移動資料的技術。DMS 可讓計算節點存取聯結和彙總所需的資料。DMS 不是 Azure 服務。它是一項在所有節點上與 SQL Database 並排執行的 Windows 服務。因為 DMS 會在幕後執行，所以您不會與它直接互動。不過，當您查看查詢計劃時，您會發現這類計劃包含一些 DMS 作業，因為資料移動必須為某種圖形或形式才能平行執行每項查詢。


### 最佳化的查詢效能

除了分治策略，MPP 方法也由許多資料倉儲特定效能最佳化輔助，包括：

- 一項分散式查詢最佳化工具和一組複雜的統計資料。使用資料大小和散發的資訊，服務就能夠藉由評估特定分散式查詢作業的成本來最佳化查詢。

- 整合到資料移動程序中的進階演算法和技術，能夠視需要在計算資源之間有效率地移動資料以執行查詢。這些資料移動作業均為內建，且資料移動服務的所有最佳化都會自動進行。

- 預設的叢集資料行存放區索引。與傳統的資料列導向儲存體相比，使用資料行儲存體的 SQL 資料倉儲最多可讓壓縮速度提升 5 倍，查詢效能提升 10 倍。需要掃描大量資料列的分析查詢適合使用資料行存放區索引。

## 可調整

SQL 資料倉儲的架構引進分隔的儲存體和計算功能，可單獨進行調整。SQL Database 快速又簡單的部署結構允許即時的額外計算。輔助這項功能就是使用 Azure 儲存體 Blob。使用 Blob 不只提供穩定、複寫儲存體，同時也提供低成本的輕鬆擴充基礎結構。SQL 資料倉儲運用雲端規模儲存體和 Azure 計算的組合，讓您只在需要時支付查詢效能儲存體的費用。變更計算數量就像是在 Azure 入口網站中將滑桿向左或向右移動一樣簡單，但是也可以使用 T-SQL 和 PowerShell 排定。

SQL 資料倉儲提供獨立於儲存體且可完全控制計算數量的功能，可讓您完全暫停資料倉儲。在保留您的儲存體位置的同時，所有計算會釋放給 Azure 的主集區，立即為您節省費用。有需要時，只要繼續計算，並讓工作負載使用資料和計算即可。

SQL 資料倉儲的計算使用量是使用 SQL 資料倉儲單位 (DWU) 測量。DWU 是您的資料倉儲擁有的基礎能力測量，設計來確保您在任何指定的時間擁有與您的倉儲相關聯的標準效能數量。具體而言，我們使用 DWU 以確保：

- 您可以有效地調整您的資料倉儲，而不必擔心基礎硬體或軟體。

- 您在變更資料倉儲的大小之前，可以透過查看 DWU 層級以了解效能。

- 可以變更或移動您的執行個體的基礎硬體與軟體，而不會影響工作負載效能

- 我們可以調整服務的基礎架構，而不會影響您的工作負載的效能。

- 當我們快速提升 SQL 資料倉儲的效能時，我們可以確保是以可調整及平均影響系統的方式執行這項操作。

### 資料倉儲單位

具體而言，我們將資料倉儲單位視為三個精確度量的量值，我們發現是與資料倉儲工作負載效能高度相互關聯。我們對於一般可用性的目標是這些關鍵工作負載度量會以您針對資料倉儲選擇的 DWU 進行線性調整。

**掃描/彙總**：此工作負載度量會採用標準資料倉儲查詢，該查詢會掃描大量資料列，然後執行複雜的彙總。這是 IO 和 CPU 密集作業。

**負載**：此度量會測量將資料擷取至服務的能力。PolyBase 從 Azure 儲存體 Blob 載入具代表性的資料集時完成負載。這項度量是設計來對服務的網路和 CPU 層面給予壓力。

**CREATE TABLE AS SELECT (CTAS)**：CTAS 會測量建立資料表複本的能力。這牽涉到從儲存體讀取資料、跨應用裝置的節點散發，以及重新寫入至儲存體。這是 CPU 和網路密集作業。

### 調整時機

整體來說，我們想要讓 DWU 簡單。當您需要更快的結果時，提升您的 DWU 並且為較高的效能支付款項。當您需要較小的計算能力時，減少您的 DWU 並且僅針對您需要的項目支付款項。有時候您可能會想要在以下時機變更 DWU 的數目：

- 不需要執行查詢時 (也許是晚上或週末)，可暫停計算資源以取消所有正在執行的查詢，並移除所有已配置給資料倉儲的 DWU。

- 執行大量資料載入或轉換作業時，建議您相應增加以使您的資料更快速可供使用。

- 為了要了解您理想的 DWU 值，嘗試在載入您的資料之後相應增加和相應減少並執行幾個查詢。由於調整很快速，您可以嘗試一些不同層級的效能，不用超過一小時即可得知。

> [AZURE.NOTE] 請注意，由於 SQL 資料倉儲的架構，您可能不會在較低的資料量中看到預期的效能功能。我們建議從等於或大於 1 TB 的資料量開始，以便取得真正的效能優勢指標。

## 整合式

SQL 資料倉儲以 SQL Server 的實證關聯式資料庫引擎為基礎，且包含企業資料倉儲的許多功能。如果您已經熟悉 Transact-SQL，則可輕鬆地將您的知識傳輸到 SQL 資料倉儲。無論您是進階或新手使用者，文件範例都能夠協助您開始著手。整體來說，您可以考慮我們建構 SQL 資料倉儲的語言元素的方式，如下所示：

- SQL 資料倉儲針對許多作業使用 SQL Server 的 Transact-SQL (TSQL) 語法，並且支援一組廣泛的傳統 SQL 建構，例如預存程序、使用者定義函數、資料表資料分割、索引和定序。

- SQL 資料倉儲也包含一些最新的 SQL Server 功能，包括叢集資料行存放區索引、PolyBase 整合和資料稽核 (完整的威脅評估)。

- SQL 資料倉儲仍處於開發階段，較不常用於資料倉儲工作負載或是比 SQL Server 還新的特定 TSQL 語言元素目前可能無法使用。請參閱我們的移轉文件以取得與此問題相關的詳細資訊。

透過 SQL Server、SQL 資料倉儲、SQL Database 和「分析平台系統」之間的 Transact-SQL 與功能共通性，您能夠開發符合您資料需求的解決方案。您可以根據效能、安全性和調整需求，決定儲存資料的位置，然後視需要在不同系統之間傳輸資料。

除了採用 SQL Server 的 TSQL 介面區，SQL 資料倉儲也整合了許多 SQL Server 使用者可能很熟悉的工具。具體而言，我們著重於與 SQL 資料倉儲的幾個類別的工具整合，包括：

**傳統的 SQL Server 工具**：與 SQL Server Analysis Services、Integration Services 和 Reporting Services 的完整整合可用於 SQL 資料倉儲。

**以雲端為基礎的工具**：SQL 資料倉儲可以與 Azure 中的數個新工具一起使用，並且已與 Azure 的 Data Factory、串流分析、機器學習服務和 Power BI 緊密整合。

**協力廠商工具**：許多協力廠商工具提供者與 SQL 資料倉儲整合的工具都已經過認證。請參閱完整清單。

## 混合式

搭配 PolyBase 使用 SQL 資料倉儲可以給予使用者前所未有的能力，在其生態系統之間移動資料，解除鎖定能力以設定具有非關聯式與內部部署資料來源的進階混合式案例。

Polybase 易於使用，且可讓您透過使用相同的熟悉 T-SQL 命令運用不同來源的資料。Polybase 讓查詢 Azure blob 儲存體中的非關聯式資料就像查詢一般資料表般容易。使用 Polybase 即可查詢非關聯式資料，或將非關聯式資料匯入 SQL 資料倉儲。

- PolyBase 使用外部資料表存取非關聯式資料。資料表定義會儲存在 SQL 資料倉儲中，可以由 SQL 和像是存取一般關聯式資料一樣的工具存取。

- 在其整合中無從得知 Polybase。它會為其支援的所有來源提供相同的功能。Polybase 可讀取各種格式的資料，包括分隔檔案或 ORC 檔案。

- PolyBase 可用來存取 Blob 儲存體，該儲存體也用來做為 HD Insight 叢集的儲存體，讓您使用關聯式與非關聯式工具，以最新的方式存取相同的資料。

## 後續步驟

現在您已稍微了解 SQL 資料倉儲，請深入了解[資料倉儲工作負載]、[如何佈建] SQL 資料倉儲，以及[如何載入範例資料]。或者，也可以看一下其中一些其他 SQL 資料倉儲資源。

- [部落格] 
- [功能要求]
- [影片]
- [CAT 小組部落格]
- [建立支援票證]
- [MSDN 論壇]
- [Stack Overflow 論壇]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[建立支援票證]: ./sql-data-warehouse-get-started-create-support-ticket.md
[資料倉儲工作負載]: ./sql-data-warehouse-overview-workload.md
[如何載入範例資料]: ./sql-data-warehouse-get-started-manually-load-samples.md
[如何佈建]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->
[部落格]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 小組部落格]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 論壇]: https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureSQLDataWarehouse
[Stack Overflow 論壇]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[影片]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0511_2016-->