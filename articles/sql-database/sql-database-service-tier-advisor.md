---
title: "Azure SQL Database 定價層建議"
description: "在 Azure 入口網站中變更定價層時，提供的各種定價層建議所建議的層是最適合執行現有 Azure SQL Database 的工作負載。 定價層會說明 SQL Database 的服務層級和效能等級。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 265beec9-7478-4abd-9ab5-4094e2e75fe4
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/08/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 1596e1d146af13fba965a0db45c2d9bbc0c6f6be


---
# <a name="sql-database-pricing-tier-recommendations"></a>SQL Database 定價層建議
 定價層建議會針對最適合執行現有 Azure SQL Database 工作負載的服務層和效能等級提供建議。

> [!NOTE]
> 定價層建議僅適用於 Web 和 Business 資料庫，以及彈性資料庫集區，而且只有在 [Azure 入口網站](https://portal.azure.com/)中才可看到建議。
>
>

經由下列工作取得定價層建議：

* [變更 SQL Database 的服務層級和效能等級 (定價層)](sql-database-scale-up.md)
* [將 Azure SQL Server 升級至 V12](sql-database-upgrade-server-portal.md)
* 瀏覽至您的 V12 伺服器。 請參閱 [SQL Database 定價層建議](sql-database-service-tier-advisor.md)。
* [建立彈性資料庫集區](sql-database-elastic-pool.md#which-databases-go-in-a-pool)

## <a name="overview"></a>Overview
SQL Database 服務會評估 SQL Database 的歷程記錄資源使用狀況，藉此分析目前的效能和功能需求。 此外，還會根據資料庫的大小決定至少可接受的服務層，並且啟用 [商務持續性](sql-database-business-continuity.md) 功能。

然後 Azure 會分析這項資訊，並且建議最適合的服務層和效能等級，以用於執行資料庫典型工作負載和維護其目前功能集。

* 服務會檢查歷程記錄前 15 到 30 天的資料 (資源使用量、資料庫大小和資料庫活動)，並且在耗用的資源量和目前可用服務層和效能等級的實際限制之間進行比較。
* 資料會以 15 秒鐘的間隔進行分析，每個間隔的結果集會分成最適合用來處理該結果集之工作量的現有服務層和效能等級。
* 接著這些 15 秒鐘的樣本會彙總成更大的 15-30 天分析，然後建議可以最佳化處理 95% 的歷程記錄工作負載的服務層和效能等級。

### <a name="recommendations"></a>建議
根據您的資料庫使用方式，目前可能會有 2 種建議：

| 建議 | 說明 |
|:--- |:--- |
| 升級 |升級至新的層。 |
| 無法使用 |資料庫需要最小的工作負載或大約 35 天的活動。 沒有足夠的資料可提供有效的建議。 |

## <a name="getting-pricing-tier-recommendations"></a>取得定價層建議
如要取得定價層建議，請選取現有的 Web 或商務資料庫，然後按一下 [所有設定]，再按一下 [定價層 (級別 DTU)]。 (當您[將 Azure SQL Server 升級至 V12](sql-database-upgrade-server-portal.md) 時也會獲得定價層建議。)

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [瀏覽] >  [SQL Database]。
3. 在 [SQL 資料庫]  刀鋒視窗中，按一下您想要查看建議的資料庫：

    ![選取資料庫][1]
4. 在資料庫刀鋒視窗中，選取 [所有設定]，然後選取 [定價層 (級別 DTU)]。
5. [建議的定價層] 隨即開啟，讓您可以按一下上面的建議定價層，然後按一下 [選取] 按鈕來變更至該定價層。

    ![註冊預覽版][4]
6. (選擇性) 按一下 [檢視使用量詳細資料] 以開啟 [定價層建議詳細資料] 刀鋒視窗，您可以在此檢視建議用於資料庫的層、目前層和建議層之間的功能比較，以及歷程記錄資源使用狀況分析的圖表。

    ![註冊預覽版][5]

## <a name="summary"></a>摘要
定價層建議能夠自動收集每個 SQL Database 的遙測資料，並且根據資料庫的實際效能需求和功能需求建議最佳的服務層/效能等級組合。 只要按一下 [設定] 刀鋒視窗上的 [定價層 (級別 DTU)]  ，就能查看所有 Web 和商務資料庫的建議定價層。

## <a name="next-steps"></a>後續步驟
視特定資料庫的詳細資料而定，系統通常不會立即執行升級或降級。 入口網站會在資料庫轉換到新層時提供通知，或者您可以藉由查詢 SQL Database Server 的主資料庫中的 [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) 檢視，來監視升級狀態。

<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png



<!--HONumber=Nov16_HO3-->


