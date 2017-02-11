---
title: "使用 Azure PowerShell 在 HDInsight 中建立 Windows 型 Hadoop 叢集| Microsoft Docs"
description: "了解如何使用 Azure PowerShell 建立 Azure HDInsight 的叢集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a365e67d-55bc-476e-a126-68f0962ec5aa
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/10/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: 823ba08cfc805000871e0c2a6375306f435b7910


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>使用 Azure PowerShell 在 HDInsight 中建立 Windows 型 Hadoop 叢集
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure PowerShell 建立 HDInsight 叢集。 Azure PowerShell 是一個模組，提供各種 Cmdlet，讓您透過 Windows PowerShell 管理 Azure。 如需其他叢集建立工具和功能的資訊，請按一下此頁面頂端的索引標籤，或參閱 [叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

## <a name="prerequisites"></a>必要條件：
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

開始執行本文中的指示之前，您必須擁有以下項目：

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell。

    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>存取控制需求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>建立叢集
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。 本節提供如何使用 Azure PowerShell 建立 HDInsight 叢集的指示。 如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱 [安裝並設定 Azure PowerShell](../powershell-install-configure.md)。 如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱 [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。 如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/dn858087.aspx)。

以下是使用 Azure PowerShell 建立 HDInsight 叢集時所需執行的程序：

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

## <a name="create-clusters-using-arm-template"></a>使用 ARM 範本建立叢集
您可以使用 Azure PowerShell 來部署可建立 HDInsight 叢集的 ARM 範本。  請參閱[使用 Azure PowerShell 呼叫範本](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-powershell)。

## <a name="customize-clusters"></a>自訂叢集
* 請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
* 請參閱 [使用指令碼動作自訂 Windows 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。

## <a name="next-steps"></a>後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。 若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - 了解如何開始使用 HDInsight 叢集
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
* [使用 PowerShell 管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-powershell.md) - 了解如何使用 Azure PowerShell 處理 HDInsight
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation] - 探索 HDInsight SDK

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx



<!--HONumber=Nov16_HO3-->


