<properties
   pageTitle="雲端中的 Batch 和 HPC 工作負載的資源 | Microsoft Azure"
   description="列出技術資源協助您在 Azure 中執行大規模平行、批次和高效能運算 (HPC) 工作負載。"
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="06/08/2016"
   ms.author="danlep"/>

# Azure 中的大量運算：批次和高效能運算 (HPC) 的技術資源
這是技術資源的指南，將幫助您在 Azure 中執行您的大規模平行、批次和 HPC 工作負載。使用各種 Azure 服務擴充您現有的批次或 HPC 工作負載至 Azure 雲端，或在 Azure 中建置新的大量計算解決方案。

## 方案選項

了解在 Azure 中的大量計算選項，並為您的工作負載和商務需求選擇正確的方法。

* [批次和 HPC 解決方案](batch-hpc-solutions.md)

* [影片：Azure 和 HPC 在雲端的大量運算](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](https://azure.microsoft.com/services/batch/) 是一種平台服務，可以輕鬆地從雲端啟用 Linux 和 Windows 應用程式，不必設定和管理叢集及工作排程器即可執行工作。使用 SDK 透過各種不同語言、接移資料至 Azure和建立工作執行管線，將用戶端應用程式與 Azure 批次整合。

* [說明文件](https://azure.microsoft.com/documentation/services/batch/)

* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)、[Python](http://azure-sdk-for-python.readthedocs.io/latest/)、[Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)、[Java](http://azure.github.io/azure-sdk-for-java/) 和 [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 參考

* [Batch 管理 .NET 程式庫](https://msdn.microsoft.com/library/mt463120.aspx)參考

* 教學課程：開始使用[適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md)和 [Batch Python 用戶端](batch-python-tutorial.md)

* [批次論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=azurebatch)

* [批次影片](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC 叢集解決方案

部署或擴充您現有的 Windows 或 Linux HPC 叢集至 Azure，以執行您的計算密集工作負載。

### Microsoft HPC Pack

HPC Pack 是建置在 Microsoft Azure 和 Windows Server 技術上的 Microsoft 免費 HPC 解決方案，可執行 Windows 和 Linux HPC 工作負載。

* [下載 HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [說明文件](https://technet.microsoft.com/library/jj899572.aspx)


* Azure 的 [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) 和 [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) HPC Pack 叢集選項

* [使用 HPC Pack 將量擴大到 Azure 背景工作執行個體](https://technet.microsoft.com/library/gg481749.aspx)

* [使用 HPC Pack 將量擴大到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)


* [Windows HPC 論壇](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux 與 OSS 叢集解決方案

使用這些 Azure 範本來部署 Linux HPC 叢集。

* [加速 SLURM 叢集](https://azure.microsoft.com/documentation/templates/slurm/)和[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [加速扭力叢集](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Intel Cloud Edition for Lustre Software - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) 是為了在 Windows 平台上開發及執行平行應用程式，Microsoft 實作的訊息傳遞介面標準。最新版為 MS-MPI v7。


* [下載 MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI 參考資料](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI 論壇](https://social.microsoft.com/Forums/zh-TW/home?forum=windowshpcmpi)

## 計算密集型執行個體

Azure 提供[各種大小](../virtual-machines/virtual-machines-windows-sizes.md) \(包括能夠連接到後端 RDMA 網路的計算密集型 [A8 和 A9](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) 執行個體) 來執行您的 Linux 和 Windows HPC 工作負載。


* [設定 Linux RDMA 叢集以執行 MPI 應用程式](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)

* [使用 Microsoft HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)



## 範例和示範

* [Azure Batch C# 和 Python 程式碼範例](https://github.com/Azure/azure-batch-samples)

* [測試磁碟機 SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## 相關的 Azure 服務

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [機器學習服務](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [虛擬機器擴展集](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)

* [雲端服務](https://azure.microsoft.com/documentation/services/cloud-services/)

* [媒體服務](https://azure.microsoft.com/documentation/services/media-services/)

## 架構藍圖

* [HPC and data orchestration using Azure Batch and Azure Data Factory (使用 Azure Batch 和 Azure Data Factory 的 HPC 及資料協調)](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) 和[文章](../data-factory/data-factory-data-processing-using-batch.md)

## 產業方案

* [銀行與資本市場](https://finance.azure.com/)

* [工程模擬](https://simulation.azure.com/)

## 客戶案例

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

* [明德精算顧問有限公司](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



## 後續步驟

* 最新宣告請參閱 [Microsoft HPC 和批次的小組部落格](http://blogs.technet.com/b/windowshpc/)以及[Azure 部落格](https://azure.microsoft.com/blog/tag/hpc/)。
* 另請參閱[批次的新功能](https://azure.microsoft.com/updates/?service=batch)或訂閱 [RSS 摘要](https://azure.microsoft.com/updates/feed/?service=batch)。

<!---HONumber=AcomDC_0615_2016-->