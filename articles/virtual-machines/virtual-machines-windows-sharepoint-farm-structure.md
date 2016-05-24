<properties
	pageTitle="Azure 中的 SharePoint Server 2013 伺服器陣列 | Microsoft Azure"
	description="找說明如何在 Microsoft Azure 中設定開發/測試環境或生產的 SharePoint Server 2013 伺服器陣列的文章。"
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
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# 在 Azure 基礎結構服務中架設的 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

在 Microsoft Azure 基礎結構服務中，設定第一個或下一個開發/測試或實際執行 SharePoint Server 2013 伺服器陣列，這樣您就可以利用簡化設定的優點，還能夠快速擴大伺服器陣列，使其增加新的容量或最佳化關鍵功能。

## 基本的 SharePoint 開發/測試伺服器陣列

這個自動建立的環境包含純雲端 Azure 虛擬網路中的三部伺服器：網域控制站、SQL Server 和 SharePoint 伺服器。

請參閱 Azure 入口網站的 Azure Marketplace 中的 [SharePoint 2013 非 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)項目。這會為連結網際網路的 SharePoint 網站建立基本開發/測試伺服器陣列。如需其他詳細資訊，請參閱[建立 SharePoint 伺服器陣列](virtual-machines-windows-sharepoint-farm.md)。

您也可以使用 Azure Resource Manager 範本。請參閱 [SharePoint](virtual-machines-linux-app-frameworks.md)。

> [AZURE.NOTE] Azure 入口網站的 Azure Marketplace 中的 **SharePoint 伺服器陣列**項目已移除。

## 高可用性 SharePoint 開發/測試伺服器陣列

這個自動建立的環境包含純雲端 Azure 虛擬網路中的九部伺服器：兩部用於網域控制站、三部用於 SQL Server 叢集、兩部應用程式層的 SharePoint 伺服器，以及兩部 Web 層的 SharePoint 伺服器。

請參閱 Azure 入口網站的 Azure Marketplace 中的 [SharePoint 2013 HA 伺服器陣列](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)項目。這會為網際網路對向的 SharePoint 網站建立高可用性的開發/測試伺服器陣列。如需其他詳細資訊，請參閱[建立 SharePoint 伺服器陣列](virtual-machines-windows-sharepoint-farm.md)。

您也可以使用 Azure Resource Manager 範本。請參閱[部署一個包含九部伺服器的 SharePoint 伺服器陣列](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm)。

> [AZURE.NOTE] Azure 入口網站的 Azure Marketplace 中的 **SharePoint 伺服器陣列**項目已移除。

## 混合式雲端開發/測試伺服器陣列

利用 [混合式雲端開發/測試環境中的 SharePoint 內部網路伺服器陣列](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), ，您可以建立模擬的混合式雲端設定 (就可以架設簡易雙層式 SharePoint 伺服器陣列)，用來從您在網際網路上的位置，測試 Azure 中架設的內部網路 SharePoint 伺服陣列。

## 高可用性、內部網路 SharePoint 實際執行伺服器陣列

部署 [具有 Azure 中 SQL Server AlwaysOn 可用性群組的 SharePoint 2013](virtual-machines-windows-sp-intranet-overview.md) 後，即表示您在 Azure 中建置了可立即實際執行、高可用性的內部網路 SharePoint Server 2013 伺服器陣列。

## 下一步

- 探索 Azure 基礎結構服務中其他的 [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) 組態。

<!---HONumber=AcomDC_0511_2016-->