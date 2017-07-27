---
title: "適用於 SQL Database 的 Azure PowerShell 指令碼範例 | Microsoft Docs"
description: "Azure PowerShell 指令碼範例可協助您建立和管理 Azure SQL Database 伺服器、彈性集區、資料庫和防火牆。"
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c7daedd68992402ad2b165a95c263d3659a3ea1a
ms.contentlocale: zh-tw
ms.lasthandoff: 06/28/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure SQL Database 的 Azure PowerShell 範例

下表包含適用於 Azure SQL Database 之範例 Azure PowerShell 指令碼的連結。

| |  |
|---|---|
|**建立單一資料庫和彈性集區**||
| [建立單一資料庫並設定防火牆規則](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立單一 Azure SQL Database，並設定伺服器層級防火牆規則。 |
| [建立彈性集區並移動集區資料庫](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會建立 Azure SQL Database 彈性集區、移動集區資料庫，並變更效能層級。|
|**設定異地複寫和容錯移轉**||
| [使用作用中異地複寫設定單一資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會為單一 Azure SQL Database 設定作用中異地複寫，並將其容錯移轉到次要複本。 |
| [使用作用中異地複寫設定集區資料庫並進行容錯移轉](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會為 SQL 彈性集區中的 Azure SQL Database 設定作用中異地複寫，並將其容錯移轉到次要複本。 |
| [設定單一資料庫的容錯移轉群組並進行容錯移轉 (預覽)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會為 Azure SQL Database 伺服器執行個體設定容錯移轉群組，並將資料庫新增到容錯移轉群組，然後將其容錯移轉到次要伺服器。 |
|**調整單一資料庫和彈性集區**||
| [調整單一資料庫](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會監視 Azure SQL Database 的效能計量，並將其調整為較高的效能層級，然後對其中一個效能計量建立警示規則。 |
| [調整彈性集區](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 指令碼會監視 Azure SQL Database 彈性集區的效能計量，並將其調整為較高的效能層級，然後對其中一個效能計量建立警示規則。  |
| **稽核與威脅偵測** |
| [設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會設定 Azure SQL Database 的稽核與威脅偵測原則。 |
| **還原、複製和匯入資料庫**||
| [還原資料庫](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會從異地備援備份還原 Azure SQL Database，並將已刪除的 Azure SQL Database 還原為最新的備份。 |
| [將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會在新的 Azure SQL Server 中建立現有 Azure SQL Database 的複本。 |
| [從 bacpac 檔案匯入資料庫](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會從 bacpac 檔案將資料庫匯入 Azure SQL Server。 |
|||

