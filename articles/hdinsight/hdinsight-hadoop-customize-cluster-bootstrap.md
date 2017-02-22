---
title: "使用 Bootstrap 自訂 HDInsight 叢集 | Microsoft Docs"
description: "了解如何使用 Bootstrap 自訂 HDInsight 叢集。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a2b32f23381ed1f9912edf6432f029e51bdf1be4
ms.openlocfilehash: 0bb7af25211ef728055ddb5a6fb826411986dd4e


---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>使用 Bootstrap 自訂 HDInsight 叢集
有時候，您需要設定包含下列項目的組態檔：

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

叢集無法保留重新製作映像所造成的變更。 如需重新製作映像的詳細資訊，請參閱 [Role Instance Restarts Due to OS Upgrades (角色執行個體因作業系統升級而重新啟動)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。 若要在叢集存留期間保留變更，您可以在建立程序期間使用 HDInsight 叢集自訂。 我們建議您用這個方法來變更叢集的組態，並讓組態在這些 Azure 重新安裝映像、重新啟動、重新開始事件的過程中，都能保持不變。 這些組態變更會在服務啟動之前套用，因此服務不需要重新啟動。 

有 3 個方法可以使用 Bootstrap：

* 使用 Azure PowerShell
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* 使用 .NET SDK
* 使用 Azure Resource Manager 範本

如需於建立期間在 HDInsight 叢集上安裝其他元件的相關資訊，請參閱：

* [使用指令碼動作來自訂 HDInsight 叢集| Azure (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
* [使用指令碼動作來自訂 HDInsight 叢集 (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
下列 PowerShell 程式碼會自訂 Hive 組態：

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

可完整運作的 PowerShell 指令碼可在 [附錄 A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample)中找到。

**若要確認變更：**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左窗格上按一下 [瀏覽]，然後按一下 [HDInsight 叢集]。
3. 按一下您剛才使用 PowerShell 指令碼建立的叢集。
4. 在刀鋒視窗頂端按一下 [儀表板]  ，以開啟 Ambari UI。
5. 按一下左側功能表中的 [Hive]  。
6. 按一下 [Summary (摘要)] 中的 [HiveServer2]。
7. 按一下 [Configs (設定)]  索引標籤。
8. 按一下左側功能表中的 [Hive]  。
9. 按一下 [Advanced (進階)]  索引標籤。
10. 向下捲動，然後展開 [Advanced hive-site (進階 Hive 網站)] 。
11. 在此區段中尋找 **hive.metastore.client.socket.timeout** 。

以下是更多自訂其他組態檔的範例：

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

如需詳細資訊，請參閱 Azim Uddin 的部落格中，標題為「 [自訂 HDInsight 叢集建立](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)」的文章。

## <a name="use-net-sdk"></a>使用 .NET SDK
請參閱[在 HDInsight 中使用 .NET SDK 建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap)。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
Resource Manager 範本中，您可以使用啟動程序︰

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![HDInsight Hadoop 自訂叢集啟動程序 Azure Resource Manager 範本](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>另請參閱
* [在 HDInsight 中建立 Hadoop 叢集][hdinsight-provision-cluster]提供如何使用其他自訂選項建立 HDInsight 叢集的指示。
* [開發 HDInsight 的指令碼動作指令碼][hdinsight-write-script]
* [在 HDInsight 叢集上安裝和使用 Spark][hdinsight-install-spark]
* [在 HDInsight 叢集上安裝和使用 R][hdinsight-install-r]
* [在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install.md)。
* [在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "叢集建立期間的階段"

## <a name="appx-a-powershell-sample"></a>附錄 A：PowerShell 範例
此 PowerShell 指令碼會建立 HDInsight 叢集，並自訂 Hive 設定：

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion



<!--HONumber=Feb17_HO1-->


