<properties
	pageTitle="使用 Azure 入口網站管理 Azure SQL Database"
	description="了解如何在 Azure 傳統入口網站中使用 Azure 入口網站管理雲端中的關聯式資料庫。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="05/20/2016"
	ms.author="sstein"/>


# 使用 Azure 入口網站管理 Azure SQL Database


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

[Azure 入口網站](https://portal.azure.com/)可讓您建立、監視和管理 Azure SQL Database 與邏輯伺服器。這篇文章強調一些較常見的工作。

![資料庫概觀](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\.資料庫管理動作

![資料庫管理動作](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure 入口網站提供一組常用的資料庫動作，您可以從資料庫刀鋒視窗頂端存取這些動作。您可以將資料庫還原至先前的時間點、在 Visual Studio 中開啟資料庫、將資料庫複製到新的伺服器，以及將資料庫匯出至 Azure 儲存體帳戶。

- [還原 SQL 資料庫](sql-database-user-error-recovery.md)
- [在 Visual Studio 中開啟 SQL Database](sql-database-connect-query.md)
- [匯出 SQL Database](sql-database-export.md)

## 2\.資料庫監視

![資料庫監視](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL Database 根據預設採用的監視圖表有資料庫交易單位 (DTU)、資料庫大小和連接的健康情況。您可以自訂這些監視圖表，並延伸成其他圖表，例如 CPU 百分比、資料 IO 百分比、死結數、記錄 IO 百分比，甚至是遭防火牆封鎖之要求的百分比。

此外，您還可以設定警示規則以監視指定的度量，而且當達到預設的臨界值時，會對指定的管理員和共同管理員發出警示。

## 3\.資料庫安全性與稽核

![資料庫安全性](./media/sql-database-manage-portal/sqldatabase_security.png)

Azure SQL Database 可設為追蹤所有資料庫事件，然後將事件寫入您 Azure 儲存體帳戶中的稽核記錄。此功能有助於維持符合法規、了解資料庫活動，以及深入洞察可能代表業務考量或疑似違反安全性的不一致之處。

- [SQL Database 稽核](sql-database-auditing-get-started.md)

Azure SQL Database 也可以設定為對沒有權限的使用者遮罩處理機密資料。

- [動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)


## 4\.異地複寫

![異地複寫](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Azure SQL Database 可以設定為以非同步方式，將已認可的交易複寫至次要資料庫。在入口網站上的異地複寫部分，可讓您選取想要放置次要資料庫的 Azure 區域。

- [異地複寫](sql-database-geo-replication-overview.md)



## 其他資源

- [SQL Database](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0525_2016-->