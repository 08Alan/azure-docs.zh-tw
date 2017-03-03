---
title: "使用 DocumentDB 線上備份與還原 | Microsoft Docs"
description: "了解如何使用 Azure DocumentDB 執行 NoSQL 資料庫的自動備份與還原。"
keywords: "備份與還原, 線上備份"
services: documentdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: raprasa
translationtype: Human Translation
ms.sourcegitcommit: b5419efbaf51476cfc662c8aa814001e2757b4b7
ms.openlocfilehash: db7b24c049153b6622f50fd9934611d48c98a1e8
ms.lasthandoff: 02/07/2017


---
# <a name="automatic-online-backup-and-restore-with-documentdb"></a>使用 DocumentDB 進行自動線上備份與還原
Azure DocumentDB 可以定期自動備份您的所有資料。 自動備份的進行不會影響 NoSQL 資料庫作業的效能或可用性。 所有備份會儲存在另一個儲存體服務中，而且這些備份會全域複寫用於為區域性災害提供復原功能。 假設您不小心刪除 DocumentDB 集合，需要資料復原或災害復原解決方案，這正是自動備份適用的案例。  

本文開頭先回顧 DocumentDB 的資料備援和可用性，接著討論備份。 

## <a name="high-availability-with-documentdb---a-recap"></a>DocumentDB 的高可用性 - 回顧
DocumentDB 設計為 [分散在全區域](documentdb-distribute-data-globally.md) – 可讓您調整多個 Azure 區域的輸送量以及原則導向的容錯移轉和透明多首頁的 API。 由於資料庫系統供應項目為 [99.99%可用性 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/)，本機磁碟會永久認可 DocumentDB 中的所有寫入，這是在用戶端認可之前，由本機資料中心內的複本仲裁認可。 請注意，DocumentDB 的高可用性仰賴本機儲存體，不需依賴任何外部儲存技術。 此外，如果您的資料庫帳戶與多個 Azure 區域相關聯，您的寫入也會在其他區域複寫。 若要在低延遲狀況下調整輸送量和存取資料，您可以有盡量多個與您的資料庫帳戶相關聯的讀取區域。 在每個讀取區域中，(複寫的) 資料會在複本集上永久保存。  

如下圖所示，單一 DocumentDB 集合是 [水平分割](documentdb-partition-data.md)。 圖中的一個一個圓圈是一個「分割」，透過複本集每個分割都有高可用性。 這是在單一 Azure 區域內的本機分散 (以 X 軸表示)。 再者，每個分割 (及其對應的複本集) 會全域分散在與您的資料庫帳戶相關聯的多個區域，例如，此圖中有三個區域 - 美國東部、美國西部和印度中部。 「分割集」是全域分散的實體，由您的資料在每個區域中的多個複本組成 (以 Y 軸表示)。 您可以替資料庫帳戶相關聯的區域指派優先順序，發生災害時 DocumentDB 會以透明方式容錯移轉至下一個區域。 您也可以手動模擬容錯移轉，以測試應用程式的端對端可用性。  

下圖說明 DocumentDB 的高度備援性。

![DocumentDB 的高度備援性](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![DocumentDB 的高度備援性](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>完整自動線上備份
糟糕，我刪掉集合或資料庫了！ 有了 DocumentDB，不只您的資料，還有資料的備份都一併具有高度備援性，可針對區域性災害進行復原。 目前，這些自動化備份大約每四個小時備份一次，並隨時儲存 2 個最新的備份。 如果不小心捨棄或損毀資料，請在 8 小時內[聯絡 Azure 支援服務](https://azure.microsoft.com/support/options/)。 

備份的進行不會影響資料庫作業的效能或可用性。 DocumentDB 會在背景中備份，不會使用您佈建的 RU 或影響效能，也不影響 NoSQL 資料庫的可用性。 

不同於儲存在 DocumentDB 內的資料，自動備份會儲存在 Azure Blob 儲存體服務。 若要保證低延遲/有效率上傳，您的備份快照會上傳到 Azure Blob 儲存體的執行個體，它位於 DocumentDB 資料庫帳戶目前的寫入區域的相同區域中。 針對區域性災難的復原功能，Azure Blob 儲存體中的每個備份資料快照透過異地備援儲存體 (GRS) 再次複寫到另一個區域。 下圖顯示，整個 DocumentDB 集合 (在此範例中，三個主要磁碟分割都在美國西部) 是在美國西部的遠端 Azure Blob 儲存體帳戶中進行備份，然後再由 GRS 複寫到美國東部。 

下圖說明 GRS Azure 儲存體中所有 DocumentDB 實體的定期完整備份。

![GRS Azure 儲存體中所有 DocumentDB 實體的定期完整備份](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>指定快照的保留期
如上所述，我們會依據我們的法規定期擷取您的資料快照，最新快照被清除之前我們會保留最多 90 天。 如果集合或帳戶被刪除，DocumentDB 會儲存最新備份 90 天。

## <a name="restore-database-from-the-online-backup"></a>從線上備份還原資料庫
如果您不小心刪除資料，可以[提出支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)或[連絡 Azure 支援](https://azure.microsoft.com/support/options/)，要求從最新的自動備份還原資料。 如果要還原特定的備份快照集，DocumentDB 會需要該快照至少是在該快照的備份週期持續時間之內。

## <a name="next-steps"></a>後續步驟
若要在多個資料中心複寫您的 NoSQL 資料庫，請參閱 [使用 DocumentDB 全域分散您的資料](documentdb-distribute-data-globally.md)。 

若要提出還原要求，請連絡 Azure 支援， [從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


