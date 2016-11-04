---
title: 在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager
description: 在 Azure Resource Manager 模式中使用 Azure 虛擬機器建立 Always On 可用性群組。 本教學課程主要是透過此使用者介面來自動建立整個解決方案。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: MikeRayMSFT

---
# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager
> [!div class="op_single_selector"]
> * [Resource Manager：範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
> * [Resource Manager︰手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
> * [傳統：UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
> * [傳統：PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)
> 
> 

<br/>

此端對端教學課程將示範如何使用 Azure Resource Manager 虛擬機器建立 SQL Server 可用性群組。 本教學課程使用 Azure 刀鋒視窗來設定範本。 逐步進行本教學課程時，您將在入口網站中檢閱預設設定、輸入必要的設定，以及更新刀鋒視窗。

在本教學課程結束時，您 Azure 中的 SQL Server 可用性群組解決方案將包含下列項目：

* 包含多個子網路 (前端和後端子網路) 的虛擬網路
* 具有 Active Directory (AD) 網域的兩個網域控制站
* 兩個 SQL Server VM 已部署至後端子網路並加入 AD 網域
* 具有節點多數仲裁模型的 3 節點 WSFC 叢集
* 具有兩份可用性資料庫同步認可複本的可用性群組

下圖將解決方案以圖形呈現。

![在 Azure 中針對 AG 測試實驗室架構](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

此解決方案中的資源全部屬於單一資源群組。

本教學課程假設您已句備下列條件：

* 您已經有 Azure 帳戶。 如果您沒有帳戶，請 [註冊一個試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。
* 您已經知道如何透過 GUI 佈建來自虛擬機器資源庫的 SQL Server VM。 如需詳細資訊，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)
* 您已非常熟悉可用性群組的功能。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)。

> [!NOTE]
> 如果您對搭配 SharePoint 使用可用性群組感興趣，另請參閱 [為 SharePoint 2013 設定 SQL Server 2012 Always On 可用性群組](http://technet.microsoft.com/library/jj715261.aspx)。
> 
> 

在本教學課程中，您將使用 Azure 入口網站執行下列動作：

* 從入口網站選取 Always On 範本
* 檢閱範本設定，並針對您的環境更新一些組態設定
* 監視 Azure 建立整個環境的情形
* 連接至其中一個網域控制站，再連接至其中一個 SQL Server

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>從資源庫佈建叢集
Azure 提供整個解決方案的資源庫映像。 若要找出範本，請執行下列動作：

1. 使用您的帳戶登入 Azure 入口網站。
2. 在 Azure 入口網站上，按一下 [+新增]。 入口網站將會開啟 [新增] 刀鋒視窗。
3. 在 [新增] 刀鋒視窗上，搜尋 **AlwaysOn**。
   ![尋找 AlwaysOn 範本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. 在搜尋結果中，找出「SQL Server AlwaysOn 叢集」 。
   ![AlwaysOn 範本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. 在 [選取部署模型] 中，選擇 [Resource Manager]。

### <a name="basics"></a>基本概念
按一下 [基本]  並設定下列各項：

* [系統管理員使用者名稱] 是具有網域系統管理員權限，且在兩個 SQL Server 執行個體上皆具備 SQL Server sysadmin 固定伺服器角色成員身分的使用者帳戶。 本教學課程使用 **DomainAdmin**。
* 是網域系統管理員帳戶的密碼。 使用複雜密碼。 確認密碼。
* 是在執行針對可用性群組部署的所有資源時，Azure 將會收費的訂用帳戶。 如果您的帳戶有多個訂用帳戶，您可以指定不同的訂用帳戶。
* [資源群組] 是本教學課程建立的所有 Azure 資源所屬群組的名稱。 本教學課程使用 **SQL-HA-RG**。 如需詳細資訊，請參閱 (Azure Resource Manager 概觀)[resource-group-overview.md/#resource-groups]。
* 是本教學課程將於其中建立資源的 Azure 區域。 選取 Azure 區域來裝載基礎結構。

以下是 [基本]  刀鋒視窗看起來的樣子：

![基本概念](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

* 按一下 [確定] 。

### <a name="domain-and-network-settings"></a>網域和網路設定
此 Azure 資源庫範本會建立新的網域與新的網域控制站。 它也會建立一個新的網路和兩個子網路。 此範本無法在現有的網域或虛擬網路中建立伺服器。 下一步是設定網域和網路設定。

在 [網域和網路設定]  刀鋒視窗上，檢閱網域和網路設定的預設值：

* [樹系根網域名稱] 是將裝載叢集的 AD 網域要使用的網域名稱。 本教學課程使用 **contoso.com**。
* [虛擬網路名稱] 是 Azure 虛擬網路的網路名稱。 本教學課程使用 **autohaVNET**。
* [網域控制站子網路名稱] 是裝載網域控制站之虛擬網路中某個部分的名稱。 本教學課程使用 **subnet-1**。 這個子網路將會使用位址首碼 **10.0.0.0/24**。
* [SQL Server 子網路名稱] 是裝載 SQL Server 和檔案共用見證之虛擬網路中某個部分的名稱。 本教學課程使用 **subnet-2**。 這個子網路將會使用位址首碼 **10.0.1.0/26**。

若要深入了解 Azure 中的虛擬網路，請參閱 [Virtual Network Overview (虛擬網路概觀)](../virtual-network/virtual-networks-overview.md)。  

[網域和網路設定]  看起來應該像這樣：

![網域和網路設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

如有必要，您可以變更這些值。 在本教學課程中，我們使用預設值。

* 檢閱設定，然後按一下 [確定] 。

### <a name="availability-group-settings"></a>可用性群組設定
在 [可用性群組設定]  上，檢閱可用性群組和接聽程式的預設值。

* [可用性群組名稱] 是可用性群組的叢集資源名稱。 本教學課程使用 **Contoso-ag**。
* [可用性群組接聽程式名稱] 是供叢集和內部負載平衡器使用。 連接到 SQL Server 用戶端可以使用這個名稱來連接到資料庫的適當複本。 本教學課程使用 **Contoso-listener**。
* [可用性群組接聽程式連接埠] 指定 SQL Server 接聽程式將使用的 TCP 連接埠。 本教學課程使用預設連接埠 **1433**。

如有必要，您可以變更這些值。 本教學課程使用預設值。  

![可用性群組設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

* 按一下 [確定] 。

### <a name="vm-size,-storage-settings"></a>VM 大小，儲存體設定
在 [VM 大小，儲存體設定]  上，選擇 SQL Server 虛擬機器大小，並檢閱其他設定。

* [SQL Server 虛擬機器大小] 是兩個 SQL Server 的 Azure 虛擬機器大小。 選擇適合您的工作負載的虛擬機器大小。 如果您要為教學課程建置此環境，請使用 **DS2**。 針對生產工作負載，請選擇可支援工作負載的虛擬機器大小。 許多生產環境工作負載將需要 **DS4** 或更大。 此範本會建置兩個此大小的虛擬機器，並在每個虛擬機器上安裝 SQL Server。 如需相關資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

> [!NOTE]
> Azure 會安裝 SQL Server Enterprise 版。 成本根據版本和虛擬機器大小而定。 如需目前成本的詳細資訊，請參閱 [虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。
> 
> 

* [網域控制站虛擬機器大小] 是網域控制站的虛擬機器大小。 本教學課程使用 **D2**。
* [檔案共用見證虛擬機器大小] 是檔案共用見證的虛擬機器大小。 本教學課程使用 **A1**。
* [SQL 儲存體帳戶] 是存放 SQL Server 資料和作業系統磁碟的儲存體帳戶的名稱。 本教學課程使用 **alwaysonsql01**。
* [DC 儲存體帳戶] 是網域控制站的儲存體帳戶名稱。 本教學課程使用 **alwaysondc01**。
* [SQL Server 資料磁碟大小 (TB)] 是 SQL Server 資料磁碟的大小，以 TB 為單位。 指定從 1 到 4 的數字。 這是會附加至每個 SQL Server 的資料磁碟的大小。 本教學課程使用 **1**。
* 會根據工作負載類型，設定 SQL Server 虛擬機器的特定儲存體組態設定。 在此案例中，所有 SQL Server 都使用進階儲存體，且 Azure 磁碟主機快取設為唯讀。 此外，有下列三種設定供您選擇，以最佳化 SQL Server 的工作負載設定：
  
  * 不會設定任何特定的組態設定
  * 會設定追蹤旗標 1117 和 1118
  * 會設定追蹤旗標 1117 和 610

本教學課程使用 [一般工作負載] 。

![VM 大小儲存體設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

* 檢閱設定，然後按一下 [確定] 。

#### <a name="a-note-about-storage"></a>儲存體注意事項
其他最佳化視 SQL Server 資料磁碟大小而定。 對於資料磁碟的每一 TB，Azure 會額外新增 1 TB 進階儲存體 (SSD)。 當伺服器需要 2 TB 以上時，該範本會在每個 SQL Server 上建立存放集區。 存放集區是一種儲存虛擬化，經由設定多張光碟來提供更高的容量、備援及效能。  然後，此範本會在存放集區上建立儲存空間，並當成單一資料呈現給作業系統。 此範本會將此磁碟指定為 SQL Server 的資料磁碟。 此範本會使用使用下列設定來調整 SQL Server 的存放集區：

* 等量磁碟區大小為虛擬磁碟的交錯設定。 針對交易式工作負載，這設定為 64 KB。 針對資料倉儲工作負載，此設定為 256 KB。
* 備援為簡單 (無備援)。

> [!NOTE]
> Premium 進階儲存體為本機備援，在單一區域內會保留三份資料，所以存放集區上不需要額外備援。
> 
> 

* 資料行計數等於存放集區內的磁碟數目。

如需有關儲存空間和存放集區的詳細資訊，請參閱：

* [儲存空間概觀](http://technet.microsoft.com/library/hh831739.aspx)。
* [Windows Server 備份和存放集區](http://technet.microsoft.com/library/dn390929.aspx)

如需有關 SQL Server 組態最佳做法的詳細資訊，請參閱 [Performance best practices for SQL Server in Azure virtual machines (Azure 虛擬機器中的 SQL Server 效能最佳做法)](virtual-machines-windows-sql-performance.md)

### <a name="sql-server-settings"></a>SQL Server 設定
在 [SQL Server 設定]  上，檢閱及修改 SQL Server VM 名稱前置詞、SQL Server 版本、SQL Server 服務帳戶和密碼，以及 SQL 自動修補維護排程。

* [SQL Server 名稱前置詞] 是用來建立每個 SQL Server 的名稱。 本教學課程使用 **Contoso-ag**。 SQL Server 名稱將是 *Contoso-ag-0* 和 *Contoso-ag-1*。
* [SQL Server 版本] 是 SQL Server 的版本。 本教學課程使用 **SQL Server 2014**。 您也可以選擇 **SQL Server 2012** 或 **SQL Server 2016**。
* [SQL Server 服務帳戶使用者名稱] 是 SQL Server 服務的網域帳戶名稱。 本教學課程使用 **sqlservice**。
* 是 SQL Server 服務帳戶的密碼。  使用複雜密碼。 確認密碼。
* [SQL 自動修補維護排程] 會識別 Azure 將自動修補 SQL Server 的工作日。 在本教學課程中，請輸入**星期日**。
* 是將開始進行自動修補的 Azure 區域當天時間。

> [!NOTE]
> 每個 VM 的修補時段會錯開一小時。 一次只修補一個虛擬機器，以避免服務中斷。
> 
> 

![SQL Server 設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

檢閱設定，然後按一下 [確定] 。

### <a name="summary"></a>摘要
在 [摘要] 頁面上，Azure 會驗證設定。 您也可以下載此範本。 檢閱摘要。 按一下 [確定] 。

### <a name="buy"></a>購買
這個最終的刀鋒視窗包含 [使用條款] 和 [隱私權原則]。 檢閱此資訊。 當您準備好讓 Azure 開始建立虛擬機器及可用性群組的所有其他必要資源時，請按一下 [建立] 。

Azure 入口網站會建立資源群組和所有資源。

## <a name="monitor-deployment"></a>監視部署
從 Azure 入口網站監視部署進度。 部署圖示會自動釘選到 Azure 入口網站的儀表板。

![Azure 儀表板](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>連接到 SQL Server
SQL Server 的新執行個體會在沒有網際網路連線的虛擬機器上執行。 不過，網域控制站確實有網際網路連線。 若要使用遠端桌面連接到 SQL Server，請先以 RDP 連接至其中一個網域控制站。 從網域控制站開啟 SQL Server 的第二個 RDP。

若要以 RDP 連接至主要網域控制站，請執行下列步驟：

1. 從 Azure 入口網站儀表板中，確認已成功部署。
2. 按一下 [資源] 。
3. 在 [資源] 刀鋒視窗中，按一下 [ad-primary-dc]，這是主要網域控制站的虛擬機器電腦名稱。
4. 在 click 。 瀏覽器會詢問您是否要開啟或儲存遠端連線物件。 按一下 [開啟] 。
   ![連接到 DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
5. 可能會警告您無法識別這個遠端連線的發行者。 按一下 [ **連接**]。
6. Windows 安全性會提示您輸入認證，以連接到主要網域控制站的 IP 位址。 按一下 [使用其他帳戶]。 在 [使用者名稱] 中，輸入 **contoso\DomainAdmin**。 這是您針對系統管理員使用者名稱而選擇的帳戶。 使用您設定範本時選擇的複雜密碼。
7. **遠端桌面** 可能會警告您因為安全性憑證有問題，無法驗證遠端電腦。 它會顯示安全性憑證名稱。 如果您依照本教學課程進行，此名稱會是 **ad-primary-dc.contoso.com**。 按一下 [是] 。

您現在已連接到主要網域控制站。 若要以 RDP 連接至 SQL Server，請遵循下列步驟：

1. 在網域控制站上，開啟 [遠端桌面連線] 。
2. 在 [電腦] 中，輸入其中一個 SQL Server 的名稱。 在本教學課程中，請輸入 **sqlserver-0**。
3. 使用您以 RDP 連接至網域控制站時所用的相同使用者帳戶和密碼。

您現在已使用 RDP 連接至 SQL Server。 您可以開啟 SQL Server Management Studio、連接到 SQL Server 的預設執行個體，並確認已設定可用性群組。

<!--HONumber=Oct16_HO2-->


