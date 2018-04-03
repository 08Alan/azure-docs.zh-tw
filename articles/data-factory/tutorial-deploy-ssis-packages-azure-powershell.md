---
title: 使用 PowerShell 將 SSIS 套件部署至 Azure | Microsoft Docs
description: 本文說明如何使用 PowerShell，將 SSIS 套件部署至 Azure 並建立 Azure-SSIS 整合執行階段。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: f9487067ae77fbb261fb683ddd15207670f9576f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure-with-powershell"></a>使用 PowerShell 將 SQL Server Integration Services 套件部署至 Azure
本教學課程提供在 Azure Data Factory 中佈建 Azure-SSIS Integration Runtime (IR) 的步驟。 接著，您可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS)，將 SQL Server Integration Services (SSIS) 套件部署到 Azure 上的此執行階段。 在本教學課程中，您會執行下列步驟：

> [!NOTE]
> 本文使用 Azure PowerShell 來佈建 Azure SSIS 整合執行階段。 若要使用 Data Factory 使用者介面 (UI) 佈建 Azure SSIS 整合執行階段，請參閱[教學課程：建立 Azure SSIS 整合執行階段](tutorial-create-azure-ssis-runtime-portal.md)。 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure-SSIS 整合執行階段
> * 啟動 Azure-SSIS 整合執行階段
> * 部署 SSIS 套件
> * 檢閱完整的指令碼

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。



## <a name="prerequisites"></a>先決條件
- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。 如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS Integration Runtime 概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。
- **Azure SQL Database 伺服器**。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 此伺服器裝載 SSIS 目錄資料庫 (SSISDB)。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 確認資料庫伺服器的 [允許存取 Azure 服務] 設定為 [開啟]。 如需詳細資訊，請參閱[保護 Azure SQL Database 資料庫](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
    - 新增用戶端電腦的 IP 位址或 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md) 
    - 確認您的 Azure SQL Database 伺服器沒有 SSIS 目錄 (SSIDB 資料庫)。 Azure-SSIS IR 的佈建不支援使用現有的 SSIS 目錄。 
- **Azure PowerShell**(英文)。 遵循[如何安裝並設定 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的指示。 您需要使用 PowerShell 來執行指令碼，以佈建在雲端中執行 SSIS 套件的 Azure-SSIS 整合執行階段。 

> [!NOTE]
> - 您可以在下列區域中建立第 2 版的資料處理站：美國東部、美國東部 2、東南亞和西歐。 
> - 您可以在下列區域中建立 Azure-SSIS IR：美國東部、美國東部 2、美國中部、北歐、西歐和澳大利亞東部。

## <a name="launch-windows-powershell-ise"></a>啟動 Windows PowerShell ISE
以系統管理權限啟動 **Windows PowerShell ISE**。 

## <a name="create-variables"></a>建立變數
複製並貼上下列指令碼：指定變數的值。 如需 Azure SQL Database 支援的**定價層**清單，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"

# You can create Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, Australia East 
$AzureSSISLocation = "EastUS"
 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>驗證資料庫的連線
新增下列指令碼來驗證您的 Azure SQL Database 伺服器 (server.database.windows.net)。 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

若要建立 Azure SQL database 作為指令碼的一部分，請參閱下列範例： 

為尚未定義的變數設定值。 例如：FirewallIPAddress。 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>登入並選取訂用帳戶
將下列程式碼新增至指令碼，以登入並選取 Azure 訂用帳戶： 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>建立資源群組
使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

如果您的資源群組已經存在，請勿將此程式碼複製到您的指令碼。 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>建立 Data Factory
執行下列命令來建立資料處理站：

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>建立整合執行階段
執行下列命令，以建立在 Azure 中執行 SSIS 套件的 Azure-SSIS 整合執行階段： 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>啟動整合執行階段
執行下列命令以啟動 Azure-SSIS 整合執行階段： 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
此命令需花費 **20 至 30 分鐘**來完成。 

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件
現在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 將您的 SSIS 套件部署至 Azure。 連線至裝載 SSIS 目錄 (SSISDB) 的 Azure SQL 伺服器。 Azure SQL 伺服器的名稱格式為：`<servername>.database.windows.net` (適用於 Azure SQL Database)。 

請參閱 SSIS 文件中的下列文章： 

- [在 Azure 上部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [連線到 Azure 上的 SSIS 目錄](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>完整指令碼
在此版本中，您必須使用 PowerShell 來佈建在雲端中執行 SSIS 套件的 Azure-SSIS 整合執行階段執行個體。 目前無法使用 Azure 入口網站佈建此執行階段。 

本節中的 PowerShell 指令碼會在雲端中設定執行 SSIS 套件的 Azure-SSIS 整合執行階段執行個體。 成功執行此指令碼之後，您就可以在 Microsoft Azure 雲端 (其中 Azure SQL Dabase 裝載 SSISDB) 中部署並執行 SSIS 套件。

1. 啟動 Windows PowerShell 整合式指令碼環境 (ISE)。
2. 在 ISE 中，從命令提示字元執行下列命令。    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. 複製本節中的 PowerShell 指令碼並貼入 ISE 中。
4. 在指令碼開頭的「Azure 的 SSIS 規格」區段中，為指令碼參數提供適當的值。 下一節將說明這些參數。
5. 執行指令碼。 指令碼結尾附近的 `Start-AzureRmDataFactoryV2IntegrationRuntime` 命令會執行 **20 至 30 分鐘**。

> [!NOTE]
> - 此指令碼會連線到 Azure SQL Database，以準備 SSIS 目錄資料庫 (SSISDB)。 若已指定，則指令碼也會設定您 VNet 的權限和設定，並將 Azure-SSIS 整合執行階段的新執行個體加入到 VNet。
> - 當您佈建 Azure-SSIS 整合執行階段的執行個體時，也會安裝適用於 SSIS 的 Azure Feature Pack 和 Access 可轉散發套件。 除了內建元件所支援的資料來源以外，這些元件還提供對 Excel 和 Access 檔案以及各種 Azure 資料來源的連線能力。 您此時無法安裝適用於 SSIS 的第三方元件 (包括來自 Microsoft 的第三方元件，例如 Attunity 所提供的 Oracle 和 Teradata 元件及 SAP BI 元件)。


如需 Azure SQL Database 支援的**定價層**清單，請參閱 [SQL Database 資源限制](../sql-database/sql-database-resource-limits.md)。 

如需 Azure Data Factory V2 和 Azure SSIS Integration Runtime 所支援的區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。 展開 [資料及分析] 以查看 [Data Factory V2] 和 [SSIS Integration Runtime]。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>將 Azure-SSIS IR 加入至 VNet
如果您使用 Azure SQL 受控執行個體 (私人預覽) 在虛擬網路 (VNet) 內裝載 SQL Server Integration Services (SSIS) 目錄，則也必須將 Azure-SSIS Integration Runtime 加入至相同的虛擬網路。 Azure Data Factory 第 2 版 (預覽版) 可讓您將 Azure-SSIS 整合執行階段加入至 VNet。 如需詳細資訊，請參閱[將 Azure-SSIS 執行階段加入至 VNet](join-azure-ssis-integration-runtime-virtual-network.md)。

如需建立可加入 VNet 之 Azure-SSIS 執行階段的完整指令碼，請參閱[建立 Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)。

## <a name="monitor-and-manage-azure-ssis-ir"></a>監視和管理 Azure-SSIS IR
請參閱下列文章中有關監視和管理 Azure-SSIS IR 的線詳細資訊。 

- [監視 Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [管理 Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 建立 Azure-SSIS 整合執行階段
> * 啟動 Azure-SSIS 整合執行階段
> * 部署 SSIS 套件
> * 檢閱完整的指令碼

進入下列教學課程，以了解如何將資料從內部部署複製到雲端： 

> [!div class="nextstepaction"]
>[複製雲端中的資料](tutorial-copy-data-dot-net.md)
