---
title: 雲端 Linux HPC Pack 叢集選項 | Microsoft Docs
description: 了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理 Linux 高效能運算 (HPC) 叢集的選項
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep

---
# 使用 HPC Pack 在 Azure 中建立及管理適用於 Linux 工作負載之 HPC 叢集的選項
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文著重於使用 HPC Pack 執行 Linux 的工作負載的選項。另有[使用 HPC Pack 執行 Windows HPC 工作負載](virtual-machines-windows-hpcpack-cluster-options.md)的選項。

## 在 Azure VM 中執行 HPC Pack 叢集
### Azure 範本
* (Marketplace) [HPC Pack cluster for Linux workloads (適用於 Linux 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (快速入門) [Create an HPC cluster with Linux compute nodes (使用 Linux 計算節點建立 HPC 叢集)](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### PowerShell 部署指令碼
* [使用 HPC Pack IaaS 部署指令碼建立 Linux HPC 叢集](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### 教學課程
* [教學課程：開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點](virtual-machines-linux-classic-hpcpack-cluster.md)
* [教學課程：在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 STAR-CCM+](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### 叢集管理
* [將工作送出至 Azure 的 HPC Pack 叢集](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
* [HPC Pack 中的作業管理](https://technet.microsoft.com/library/jj899585.aspx)

## 建立 MPI 工作負載的 RDMA 叢集
* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->