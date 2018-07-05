---
title: 規劃 Azure 檔案同步 (預覽) | Microsoft Docs
description: 了解規劃 Azure 檔案部署時的考量事項。
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 1927ab29e82836c60b2ba36c3eec0acf49778082
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335834"
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>規劃 Azure 檔案同步 (預覽) 部署
使用 Azure 檔案同步 (預覽版)，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文章說明 Azure 檔案同步部署的重要考量。 建議您另行閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)。 

## <a name="azure-file-sync-terminology"></a>Azure 檔案同步術語
在了解規劃 Azure 檔案同步部署的細節之前，請務必先了解其術語。

### <a name="storage-sync-service"></a>儲存體同步服務
儲存體同步服務是 Azure 檔案同步的最上層 Azure 資源。儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 由於儲存體同步服務可以透過多個同步群組與多個儲存體帳戶建立同步關係，因此最上層資源必須與儲存體帳戶資源不同。 一個訂用帳戶可以部署多個儲存體同步服務資源。

### <a name="sync-group"></a>同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 例如，如果您有兩組不同的檔案需要透過 Azure 檔案同步管理，您會建立兩個同步群組，並將不同的端點個別新增至這兩個同步群組。 儲存體同步服務可以視需要裝載許多同步群組。  

### <a name="registered-server"></a>已註冊的伺服器
已註冊的伺服器物件代表您的伺服器 (或叢集) 與儲存體同步服務之間的信任關係。 您可以視需要向儲存體同步服務執行個體註冊多個伺服器。 不過，一次只能向單一儲存體同步服務註冊單一伺服器 (或叢集)。

### <a name="azure-file-sync-agent"></a>Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 Azure 檔案同步代理程式有三個主要元件： 
- **FileSyncSvc.exe**：負責監視伺服器端點上之變更，並起始同步至 Azure 之工作階段的背景 Windows 服務。
- **StorageSync.sys**：負責將檔案分層處理到 Azure 檔案服務 (啟用雲端階層處理時) 的 Azure 檔案同步檔案系統篩選器。
- **PowerShell 管理 Cmdlet**：用來與 Microsoft.StorageSync Azure 資源提供者互動的 PowerShell Cmdlet。 您可以在下列 (預設) 位置找到這些 Cmdlet：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾。 如果伺服器端點的命名空間沒有重疊 (例如 `F:\sync1` 和 `F:\sync2`)，則相同磁碟區中可以存在多個伺服器端點。 您可以為每個伺服器端點個別設定雲端階層原則。 

您可以透過掛接點建立伺服器端點。 請注意，伺服器端點內的掛接點會略過。  

您可以在系統磁碟區上建立伺服器端點，但這樣做有兩個限制：
* 無法啟用雲端階層處理。
* 不會執行快速命名空間還原 (其中系統會快速中斷連線整個命名空間，然後開始重新叫用內容)。


> [!Note]  
> 僅支援非卸除式磁碟區。  不支援使用從遠端共用對應的磁碟機作為伺服器端點路徑。  此外，伺服器端點可以位於 Windows 系統磁碟區，但系統磁碟區上不支援雲端階層。

如果您將具有一組現有檔案的伺服器位置作為伺服器端點新增至同步群組，那些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

### <a name="cloud-endpoint"></a>雲端端點
雲端端點是屬於同步群組之一部分的 Azure 檔案共用。 整個 Azure 檔案共用都會同步，而且 Azure 檔案共用只能是單一雲端端點的成員。 因此，Azure 檔案共用只能是單一同步處理群組的成員。 如果您將內含一組現有檔案的 Azure 檔案共用作為雲端端點新增至同步群組，現有的檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

> [!Important]  
> Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。如需詳細資訊，請參閱 [Azure 檔案服務常見問題集](storage-files-faq.md#afs-change-detection)。

### <a name="cloud-tiering"></a>雲端階層處理 
雲端階層處理是 Azure 檔案同步的選擇性功能，可將大小大於 64 KiB 且不常使用或存取的檔案分層處理至 Azure 檔案服務。 當檔案被分層之後，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 會將本機檔案取代為指標或重新分析點。 重新分析點代表的是針對 Azure 檔案服務中檔案的 URL。 階層式檔案在 NTFS 中具有「離線」屬性集，使協力廠商應用程式得以識別階層式檔案。 當使用者開啟階層式檔案時，Azure 檔案同步會順暢地從 Azure 檔案服務重新叫用檔案資料，使用者並不需要知道檔案未儲存在本機系統上。 這項功能也稱為階層式存放裝置管理 (HSM)。

> [!Important]  
> Windows 系統磁碟區上的伺服器端點不支援雲端接層。

## <a name="azure-file-sync-interoperability"></a>Azure 檔案同步互通性 
本節涵蓋 Azure 檔案同步與 Windows Server 功能和角色，以及協力廠商解決方案的互通性。

### <a name="supported-versions-of-windows-server"></a>支援的 Windows Server 版本
Azure 檔案同步目前支援的 Windows Server 版本為：

| 版本 | 支援的 SKU | 支援的部署選項 |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter 和 Standard | 完整 (具有 UI 的伺服器) |
| Windows Server 2012 R2 | Datacenter 和 Standard | 完整 (具有 UI 的伺服器) |

Windows Server 的未來版本將會於發佈時加入支援清單。 舊版的 Windows 可能會根據使用者意見反應加入支援清單。

> [!Important]  
> 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

### <a name="file-system-features"></a>檔案系統功能
| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Azure 檔案同步會保留 Windows ACL，並由 Windows Server 在伺服器端點上強制執行。 如果檔案是直接在雲端中存取，則 Azure 檔案服務尚未支援 Windows ACL。 |
| 永久連結 | Skipped | |
| 符號連結 | Skipped | |
| 掛接點 | 部分支援 | 掛接點可能是伺服器端點的根目錄，但如果伺服器端點的命名空間中包含它們，系統會予以略過。 |
| 接合 | Skipped | 例如，分散式檔案系統 DfrsrPrivate 和 DFSRoots 資料夾。 |
| 重新分析點 | Skipped | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案會同步 (不會封鎖)，但它們會以完整檔案的形式同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，該檔案在變更下載之後就不再是疏鬆檔案。 |
| 替代資料流 (ADS) | 已保留，但未同步 | 例如，不會同步「檔案分類基礎結構」所建立的分類標籤。 每個伺服器端點上檔案的現有分類標籤會原封不動。 |

> [!Note]  
> 僅支援 NTFS 磁碟區。 不支援 ReFS、FAT、FAT32 及其他檔案系統。

### <a name="files-skipped"></a>跳過的檔案
| 檔案/資料夾 | 附註 |
|-|-|
| Desktop.ini | 系統專用檔案 |
| ethumbs.db$ | 暫存檔案的縮圖 |
| ~$\*.\* | Office 暫存檔 |
| \*.tmp | 暫存檔 |
| \*.laccdb | Access DB 鎖定檔|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 內部同步檔案|
| \\系統磁碟區資訊 | 特定磁碟區的資料夾 |
| $RECYCLE.BIN| 資料夾 |
| \\SyncShareState | 同步處理的資料夾 |

### <a name="failover-clustering"></a>容錯移轉叢集
Azure 檔案同步的 [一般用途的檔案伺服器] 部署選項支援 Windows Server 容錯移轉叢集。 「適用於應用程式資料的向外延展檔案伺服器」(SOFS) 或叢集共用磁碟區 (CSV) 上並不支援容錯移轉叢集。

> [!Note]  
> 必須在容錯移轉叢集中的每個節點上安裝 Azure 檔案同步代理程式，同步才能正確運作。

### <a name="data-deduplication"></a>重複資料刪除
針對未啟用雲端階層處理的磁碟區，Azure 檔案同步支援在磁碟區上啟用 Windows Server 重複資料刪除。 目前並不支援已啟用雲端階層處理的 Azure 檔案同步與重複資料刪除之間的互通性。

### <a name="distributed-file-system-dfs"></a>分散式檔案系統 (DFS)
從 [Azure 檔案同步代理程式 1.2](https://go.microsoft.com/fwlink/?linkid=864522) 開始，Azure 檔案同步支援與 DFS 命名空間 (DFS-N) 和 DFS 複寫 (DFS-R) 互通。

**DFS 命名空間 (DFS-N)**：DFS-N 伺服器上完全支援 Azure 檔案同步。 您可以將 Azure 檔案同步代理程式安裝在一或多個 DFS-N 成員上，同步伺服器端點與雲端端點之間的資料。 如需詳細資訊，請參閱 [DFS 命名空間概觀](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 複寫 (DFS-R)**：因為 DFS-R 與 Azure 檔案同步都是複寫解決方案，建議您在大部分情況下，以 Azure 檔案同步取代 DFS-R。不過有幾個案例，您會想要一起使用 DFS-R 和 Azure 檔案同步：

- 您正要從 DFS-R 部署移轉至 Azure 檔案同步部署。 如需詳細資訊，請參閱[將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 並非需要檔案資料複本的每個內部部署伺服器都能直接連線到網際網路。
- 分支伺服器將資料合併到您要使用 Azure 檔案同步的單一中樞伺服器。

Azure 檔案同步和 DFS-R 如需並存使用：

1. 務必在具有 DFS-R 複寫資料夾的磁碟區上停用 Azure 檔案同步雲端階層。
2. 不應在 DFS-R 唯讀複寫資料夾上設定伺服器端點。

如需詳細資訊，請參閱 [DFS 複寫概觀](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支援在已安裝 Azure 檔案同步代理程式的伺服器上使用 sysprep，而且此舉可能會導致非預期的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。

### <a name="windows-search"></a>Windows 搜尋
如果在伺服器端點上啟用雲端階層處理，則系統會略過階層式檔案，且 Windows 搜尋不會將這些檔案編製索引。 非階層式檔案則會正確編製索引。

### <a name="antivirus-solutions"></a>防毒解決方案
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒產品可能會導致階層式檔案的重新叫用。 因為階層式檔案具有「離線」屬性集，因此建議洽詢您的軟體廠商，以了解如何設定其解決方案以略過讀取離線檔案。 

下列解決方案已知支援略過離線檔案：

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) \(英文\) (請參閱 PDF 第 90 頁的 "Scan only what you need to")
- [Kaspersky 防毒程式](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>備份解決方案
備份解決方案類似防毒解決方案，可能會導致階層式檔案的重新叫用。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不要使用內部部署備份產品。

如果您要使用內部部署備份解決方案，則應該在已停用雲端階層處理的同步群組中，對其中的某個伺服器執行備份。 還原位於伺服器端點位置內的檔案時，請使用檔案層級還原選項。 所還原的檔案會同步處理至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。

> [!Note]  
> 應用程式感知、磁碟區層級和裸機 (BMR) 還原選項可能會導致非預期的結果，且目前不受支援。 未來的版本會支援這些還原選項。

### <a name="encryption-solutions"></a>加密解決方案
加密解決方案的支援取決於其實作方式。 Azure 檔案共用已知可用於：

- BitLocker 加密
- Azure 資訊保護、Azure Rights Management Services (Azure RMS) 及 Active Directory RMS

Azure 檔案共用已知無法用於：

- NTFS 加密檔案系統 (EFS)

一般來說，Azure 檔案同步應該會支援與位於檔案系統下的加密解決方案 (例如 BitLocker)，以及實作於檔案格式中的解決方案 (例如 BitLocker) 之間的互通性。 對於位於檔案系統之上的解決方案 (例如 NTFS EFS)，並沒有做出任何特殊的互通性。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="region-availability"></a>區域可用性
Azure 檔案同步僅於下列區域以預覽的形式提供：

| 區域 | 資料中心位置 |
|--------|---------------------|
| 澳洲東部 | 新南威爾斯 |
| 澳大利亞東南部 | 維多利亞 |
| 加拿大中部 | 多倫多 |
| 加拿大東部 | 魁北克市 |
| 美國中部 | 愛荷華州 |
| 東亞 | 香港 |
| 美國東部 | 維吉尼亞州 |
| 美國東部 2 | 維吉尼亞州 |
| 北歐 | 愛爾蘭 |
| 東南亞 | 新加坡 |
| 英國南部 | 倫敦 |
| 英國西部 | 卡地夫 |
| 西歐 | 荷蘭 |
| 美國西部 | California |

在預覽版中，僅支援與位於和儲存體同步服務相同之區域中的 Azure 檔案共用進行同步。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
