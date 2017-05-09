---
title: "Linux 與 Windows 之間的 Azure Service Fabric 差異 | Microsoft Docs"
description: "Linux 上的 Azure Service Fabric 預覽和 Windows 上的 Azure Service Fabric 之間的差異。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 00c1f10f7df865344233143ce9cdf546e64acca0
ms.contentlocale: zh-tw
ms.lasthandoff: 05/02/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux (預覽) 和 Windows (正式推出) 上 Service Fabric 之間的差異

由於 Linux 上的 Service Fabric 是預覽，有一些 Windows 上支援的功能在 Linux 上尚未支援。 最後，當 Linux 上的 Service Fabric 正式推出時，功能集將位於同位檢查。

* Linux 上不支援可靠的集合 (和可靠的具狀態服務)。
* ReverseProxy 無法在 Linux 上使用。
* 獨立安裝程式無法在 Linux 上使用。
* 資訊清單檔案的 XML 結構描述驗證不會在 Linux 上執行。 
* Linux 上不支援主控台重新導向。 
* 錯誤分析服務 (FAS) 無法在 Linux 上使用。
* Azure Active Directory 支援無法在 Linux 上使用。
* 某些 Powershell 命令的 CLI 命令對等用法無法使用。
* 僅可針對 Linux 叢集執行 Powershell 命令的子集 (如下一節中展開)。

>[!NOTE]
>在生產環境叢集中不支援主控台重新導向，即使是在 Windows 上亦然。

Windows 與 Linux 上的開發工具也不同。 在 Windows 上使用 VisualStudio、Powershell、VSTS 和 ETW，而在 Linux 上使用 Yeoman、Eclipse、Jenkins 和 LTTng。

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>無法針對 Linux Service Fabric 叢集運作的 Powershell Cmdlet

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>後續步驟
* [在 Linux 上準備您的開發環境](service-fabric-get-started-linux.md)
* [在 OSX 上準備您的開發環境](service-fabric-get-started-mac.md)
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Linux 上建立第一個 CSharp 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)
* [使用 Azure CLI 管理 Service Fabric 應用程式](service-fabric-azure-cli.md)

