<properties 
	pageTitle="部署企業營運應用程式 | Microsoft Azure" 
	description="在 Azure 中透過五個階段，部署包含 SQL Server AlwaysOn 可用性群組的 Web 型、高可用性企業營運應用程式。" 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# 在 Azure 中部署高可用性的企業營運應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

本文包含在 Azure 基礎結構服務中，部署包含 SQL Server AlwaysOn 可用性群組之高可用性、內部網路專屬 Web 型企業營運應用程式的逐步指示連結。應用程式會裝載於這些電腦上：

- 兩部 Web 伺服器
- 兩部資料庫伺服器
- 一部叢集多數節點伺服器
- 兩個網域控制站

以下呈現的就是這個設定，其中會為每部伺服器提供預留位置名稱。

![](./media/virtual-machines-windows-lob-overview/workload-lobapp-phase4.png)
 
每個角色至少兩部機器可確保高可用性。所有虛擬機器皆位於單一 Azure 位置 (也稱為區域)。適用於特定角色的每個虛擬機器群組都位於它們自己的可用性設定組中。

## 用料表

基準組態需要下列 Azure 服務和元件的設定組：

- 七部虛擬機器
- 四個額外資料磁碟，適用於網域控制站和執行 SQL Server 的虛擬機器。
- 三個可用性集合
- 一個跨單位虛擬網路
- 兩個儲存體帳戶

以下是適用於此組態的虛擬機器及其預設大小。

項目 | 虛擬機器描述 | 資源庫映像 | 預設大小 
--- | --- | --- | --- 
1\. | 第一網域控制站 | Windows Server 2012 R2 Datacenter | D1
2\. | 第二網域控制站 | Windows Server 2012 R2 Datacenter | D1
3\. | 主要資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
4\. | 次要資料庫伺服器 | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | D4
5\. | 叢集的多數節點 | Windows Server 2012 R2 Datacenter | D1
6\. | 第一 Web 伺服器 | Windows Server 2012 R2 Datacenter | D3
7\. | 第二 Web 伺服器 | Windows Server 2012 R2 Datacenter | D3

若要計算此組態的預估成本，請參閱 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。

1. 在 [模組] 中，按一下 [計算]，然後按一下 [虛擬機器] 數次，直到足夠建立含有七個虛擬機器的清單為止。
2. 針對每一個虛擬機器，選取：
	- 您想要的區域
	- [Windows] 類型
	- [標準] 定價層
	- 上表中的預設大小或您想要的大小來做為 [執行個體大小]

> [AZURE.NOTE] 「Azure 價格計算機」並未納入兩個執行 SQL Server 2014 Enterprise 之虛擬機器的額外 SQL Server 授權費用。如需詳細資訊，請參閱[虛擬機器價格-SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。

## 部署的階段

您可以在下列階段中部署這個設定：

- [第 1 階段：設定 Azure](virtual-machines-windows-ps-lob-ph1.md)。建立儲存體帳戶、可用性集合及跨單位虛擬網路。
- [第 2 階段：設定網域控制站](virtual-machines-windows-ps-lob-ph2.md)。建立和設定複本 Active Directory 網域服務 (AD DS) 網域控制站。
- [第 3 階段：設定 SQL Server 基礎結構](virtual-machines-windows-ps-lob-ph3.md)。建立和設定執行 SQL Server 的虛擬機器、建立叢集，以及啟用 SQL Server AlwaysOn 可用性群組。
- [第 4 階段：設定 Web 伺服器](virtual-machines-windows-ps-lob-ph4.md)。建立和設定兩部 Web 伺服器虛擬機器。
- [第 5 階段：將應用程式資料庫加入 SQL Server AlwaysOn 可用性群組](virtual-machines-windows-ps-lob-ph5.md)。準備企業營運應用程式資料庫並將其加入至 SQL Server AlwaysOn 可用性群組。

此部署是設計來搭配[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)並納入最新的建議。

這是規範性且預先定義的架構。請記住下列要點：

- 如果您是經驗豐富的 Web 型企業營運應用程式實作者，請自行決定是否要調整第 3 到 5 階段中的指示，以建置最適合您需求的應用程式基礎結構。 
- 如果您已經具備現有的 Azure 混合式雲端實作，可自行決定是否要調整或略過第 1 和 2 階段中的指示，在適當的子網路上裝載適用於新應用程式的虛擬機器。
- 所有伺服器都位於 Azure 虛擬網路中的單一子網路上。如果您想要提供其他相當於隔離子網路的安全性，可以使用 [網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

若要建置開發/測試環境或此設定的概念證明，請參閱[在混合式雲端中設定用於測試的 Web 式 LOB 應用程式](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)。

如需針對 Azure 設計 IT 工作負載的詳細資訊，請參閱[Azure 基礎結構服務實作方針](virtual-machines-linux-infrastructure-service-guidelines.md)。

## 後續步驟

- 依照[第 1 階段](virtual-machines-windows-ps-lob-ph1.md)指示開始設定此工作負載。

<!---HONumber=AcomDC_0413_2016-->