<properties
	pageTitle="在 HDInsight 上使用 Hadoop Sqoop | Microsoft Azure"
	description="了解如何從工作站使用 Azure PowerShell，在 HDInsight 叢集與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>

#在 HDInsight 上將 Sqoop 與 Hadoop 搭配使用

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何在 HDInsight 上使用 Sqoop，以進行 HDInsight 叢集與 Azure SQL Database 或 SQL Server Database 之間的匯入和匯出作業。

雖然在處理非結構化資料和半結構化資料時 (例如記錄和檔案)，很自然地會選擇 Hadoop，但有時也需要處理儲存在關聯式資料庫中的結構化資料。

[Sqoop][sqoop-user-guide-1.4.4] 是一種可在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。此工具可讓您從 SQL Server、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS)，將資料匯入 Hadoop 分散式檔案系統 (HDFS)，使用 MapReduce 或 Hive 轉換 Hadoop 中的資料，然後將資料匯回 RDBMS。在本教學課程中，您將使用 SQL Server Database 做為關聯式資料庫。

如需 HDInsight 叢集支援的 Sqoop 版本，請參閱 [HDInsight 所提供叢集版本的新功能][hdinsight-versions]。

##了解案例

HDInsight 叢集附有某些範例資料。您將用到以下兩個範例：

- 位於 */example/data/sample.log* 的 log4j 記錄檔。下列記錄擷取自此檔案：

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- 參考位於 */hive/warehouse/hivesampletable* 之資料檔案的 Hive 資料表 *hivesampletable*。此資料表包含某些行動裝置資料。

    | 欄位 | 資料類型 |
    | ----- | --------- |
    | clientid | string |
    | querytime | string |
    | market | string |
    | deviceplatform | string |
    | devicemake | string |
    | devicemodel | string |
    | state | string |
    | country | string |
    | querydwelltime | double |
    | sessionid | bigint |
    | sessionpagevieworder | bigint |

您必須先將 *sample.log* 和 *hivesampletable* 匯出至 Azure SQL Database 或 SQL Server，再使用下列路徑，將包含行動裝置資料的資料表匯回 HDInsight：

	/tutorials/usesqoop/importeddata

## 建立叢集與 SQL Database

本節說明如何建立叢集和 SQL 資料庫結構描述，以使用 Azure 入口網站和 Azure Resource Manager 範本來執行本教學課程。如果您偏好使用 Azure PowerShell，請參閱[附錄 A](#appendix-a---a-powershell-sample)。

1. 按一下以下影像，以在 Azure 入口網站中開啟 Resource Manager 範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Resource Manager 範本位於公用 Blob 容器中，*https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*。
    
    Resource Manager 範本會呼叫 Bacpac 封裝，以將資料表結構描述部署到 SQL Database。Bacpac 套件也位於公用 Blob 容器 https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac 中。如果您想要針對 Bacpac 檔案使用私用容器，請在範本中使用下列值︰
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. 從 [參數] 刀鋒視窗，輸入下列項目：

    - **ClusterName**：輸入您將建立的 Hadoop 叢集的名稱。
    - 叢集登入名稱和密碼：預設登入名稱是 admin。
    - **SSH 使用者名稱和密碼**。
    - **SQL Database 伺服器登入名稱和密碼**。

    變數區段中的下列值為硬式編碼︰
    
    |預設儲存體帳戶名稱|<CluterName>store|
    |----------------------------|-----------------|
    |Azure SQL Database 伺服器名稱|<ClusterName>dbserver|
    |Azure SQL Database 名稱|<ClusterName>db|
    
    請記下這些值。稍後在教學課程中需要這些資訊。
    
3\. 按一下 [確定] 儲存參數。

4\. 在 [自訂部署] 刀鋒視窗中，按一下 [資源群組] 下拉式方塊，然後按一下 [新增] 來建立新的資源群組。資源群組是聚集叢集、相依儲存體帳戶和其他已連結資源的容器。

5\. 按一下 [法律條款]，然後按一下 [建立]。

6\. 按一下 [建立]。您將會看到新的圖格，標題為「提交範本部署的部署」。大約需要 20 分鐘的時間來建立叢集和 SQL Database。

如果您選擇使用現有的 Azure SQL Database 或 Microsoft SQL Server

- **Azure SQL Database**：您必須設定 Azure SQL Database 伺服器的防火牆規則，以允許從您的工作站存取。如需關於建立 Azure SQL Database 和設定防火牆的指示，請參閱[開始使用 Azure SQL Database][sqldatabase-get-started]。

    > [AZURE.NOTE] 根據預設，Azure SQL Database 接受來自 Azure 服務 (例如 Azure HDInsight) 的連線。如果此防火牆設定為停用，您必須在 Azure 入口網站中加以啟用。如需關於建立 Azure SQL Database 和設定防火牆規則的指示，請參閱[建立和設定 SQL Database][sqldatabase-create-configue]。

- **SQL Server**：如果您的 HDInsight 叢集與 SQL Server 位於同一個 Azure 虛擬網路上，您可以使用本文中的步驟在 SQL Server Database 上匯入和匯出資料。

    > [AZURE.NOTE] HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。

    * 若要建立及設定虛擬網路，請參閱[虛擬網路組態工作](../services/virtual-machines/)。

        * 在您的資料中心裡使用 SQL Server 時，必須將虛擬網路設定為*站對站*或*點對站*。

            > [AZURE.NOTE] 使用**點對站**虛擬網路時，SQL Server 必須執行 VPN 用戶端組態應用程式；您可從 Azure 虛擬網路組態的 [儀表板] 存取此應用程式。

        * 在 Azure 虛擬機器中使用 SQL Server 時，只要主控 SQL Server 的虛擬機器與 HDInsight 在同一個虛擬網路中，即可使用任何虛擬網路組態。

    * 若要在虛擬網路上建立 HDInsight 叢集，請參閱[使用自訂選項在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)。

    > [AZURE.NOTE] SQL Server 也必須允許驗證。您必須使用 SQL Server 登入來完成本文中的步驟。


## 執行 Sqoop 工作

HDInsight 可以使用各種方法執行 Sqoop 工作。請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

| 如果您想要...，請**使用此方法** | ...一個**互動式**殼層 | ...**批次**處理 | ...搭配此**叢集作業系統** | ...從此**用戶端作業系統** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md) | ✔ | ✔ | Linux | Linux、Unix、Mac OS X 或 Windows |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) | &nbsp; | ✔ | Linux 或 Windows | Windows (目前) |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md) | &nbsp; | ✔ | Linux 或 Windows | Windows |

##限制

* 大量匯出 - 使用 Linux 型 HDInsight，用來將資料匯出至 Microsoft SQL Server 或 Azure SQL Database 的 Sqoop 連接器目前不支援大量插入。

* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

##後續步驟

現在，您已了解如何使用 Sqoop。若要深入了解，請參閱：

- [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
- [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
- [搭配 HDInsight 使用 Oozie][hdinsight-use-oozie]：在 Oozie 工作流程中使用 Sqoop 動作。
- [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-data]：使用 Hive 分析航班誤點資料，然後使用 Sqoop 將資料匯出至 Azure SQL Database。
- [將資料上傳至 HDInsight][hdinsight-upload-data]：尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。


## 附錄 A - PowerShell 範例

這些 PowerShell 範例會執行下列步驟：

1. 連接到 Azure。
2. 建立 Azure 資源群組。如需詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
3. 建立 Azure SQL Database 伺服器、Azure SQL Database 和兩個資料表。

	如果您改為使用 SQL Server，請使用下列陳述式來建立資料表：
	
		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

	檢查資料庫和資料表的最簡單方式是使用 Visual Studio。可以使用 Azure 入口網站檢查資料庫伺服器和資料庫。

4. 建立 HDInsight 叢集。

	若要檢查叢集，您可以使用 Azure 入口網站或 Azure PowerShell。

5. 前置處理來源資料檔。

	在本教學課程中，您會將 log4j 記錄檔 (使用分隔符號的檔案) 和 Hive 資料表匯出至 Azure SQL Database。使用分隔符號的檔案稱為 */example/data/sample.log*。在本教學課程先前的內容中，您已看過 log4j 記錄檔的幾個範例。記錄檔中有某些空白行，且有幾行類似以下內容：
	
		java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
			at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
	
	這在使用此資料的其他範例中並沒有問題，但我們必須移除這些例外狀況，才能將資料匯入 Azure SQL Database 或 SQL Server 中。如果有空白字串，或某一行的項目數少於 Azure SQL Database 資料表中定義的欄位數，Sqoop 匯出就會失敗。log4jlogs 資料表有 7 個字串類型欄位。

	此程序會在叢集上建立新檔案：tutorials/usesqoop/data/sample.log。若要檢查已修改的資料檔案，可以使用 Azure 入口網站、Azure 儲存體總管工具或 Azure PowerShell。[開始使用 HDInsight][hdinsight-get-started] 提供了使用 Azure PowerShell 來下載檔案及顯示檔案內容的程式碼範例。

6. 將資料檔案匯出至 Azure SQL Database。

	來源檔案為 tutorials/usesqoop/data/sample.log。將資料匯至其中的資料表稱為 log4jlogs。
	
	> [AZURE.NOTE] 除了連接字串資訊以外，本節中的步驟應該可運用在 Azure SQL Database 或 SQL Server 上。這些步驟已使用下列組態進行測試：
	>
	> * **Azure 虛擬網路點對站組態**：在私人資料中心裡將 HDInsight 叢集連接到 SQL Server 的虛擬網路。如需詳細資訊，請參閱[使用管理入口網站設定點對站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)。
	> * **Azure HDInsight 3.1**：如需有關在虛擬網路上建立叢集的資訊，請參閱[使用自訂選項在 HDInsight 中建立 Hadoop 叢集](hdinsight-provision-clusters.md)。
	> * **SQL Server 2014**：已設定成允許驗證，以及執行 VPN 用戶端組態套件以安全地連接到虛擬網路。

7. 將 Hive 資料表匯出至 Azure SQL Database。

8. 將 mobiledata 資料表匯入 HDInsight 叢集。

	若要檢查已修改的資料檔案，可以使用 Azure 入口網站、Azure 儲存體總管工具或 Azure PowerShell。[開始使用 HDInsight][hdinsight-get-started] 提供了使用 Azure PowerShell 來下載檔案及顯示檔案內容的程式碼範例。


### PowerShell 範例

	# Prepare an Azure SQL database to be used by the Sqoop tutorial
	
	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure Subscription ID>"
	
	$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
	$sqlDatabasePassword = "<Enter a Password>"
	
	$httpUserName = "admin"  #HDInsight cluster username
	$httpPassword = "<Enter a Password>"
	
	# used for creating Azure service names
	$nameToken = "<Enter an alias>" 
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# Used for creating tables and clustered indexes
	$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		[t1] [nvarchar](50),
		[t2] [nvarchar](50),
		[t3] [nvarchar](50),
		[t4] [nvarchar](50),
		[t5] [nvarchar](50),
		[t6] [nvarchar](50),
		[t7] [nvarchar](50))"
	
	$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
	
	$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
	[clientid] [nvarchar](50),
	[querytime] [nvarchar](50),
	[market] [nvarchar](50),
	[deviceplatform] [nvarchar](50),
	[devicemake] [nvarchar](50),
	[devicemodel] [nvarchar](50),
	[state] [nvarchar](50),
	[country] [nvarchar](50),
	[querydwelltime] [float],
	[sessionid] [bigint],
	[sessionpagevieworder][bigint])"
	
	$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{Login-AzureRmAccount}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $credential `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	
	#endregion
	
	#region - Create tables
	Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jTable
	$ret = $cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateLog4jClusteredIndex
	$cmd.ExecuteNonQuery()
	
	# Create the mobiledata table and index
	$cmd.CommandText = $cmdCreateMobileTable
	$cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	
	#endregion
	
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName)[0].Value
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $HDInsightClusterName `
		-Location $location `
		-ClusterType Hadoop `
		-OSType Windows `
		-ClusterSizeInNodes 2 `
		-HttpCredential $httpCredential `
		-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - pre-process the source file
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	# This procedure creates a new file with $destBlobName
	$sourceBlobName = "example/data/sample.log"
	$destBlobName = "tutorials/usesqoop/data/sample.log"
	
	# Define the connection string
	$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
	
	# Create block blob objects referencing the source and destination blob.
	$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
	$storageClient = $storageAccount.CreateCloudBlobClient();
	$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
	$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
	$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
	
	# Define a MemoryStream and a StreamReader for reading from the source file
	$stream = New-Object System.IO.MemoryStream
	$stream = $sourceBlob.OpenRead()
	$sReader = New-Object System.IO.StreamReader($stream)
	
	# Define a MemoryStream and a StreamWriter for writing into the destination file
	$memStream = New-Object System.IO.MemoryStream
	$writeStream = New-Object System.IO.StreamWriter $memStream
	
	# Pre-process the source blob
	$exString = "java.lang.Exception:"
	while(-Not $sReader.EndOfStream){
		$line = $sReader.ReadLine()
		$split = $line.Split(" ")
	
		# remove the "java.lang.Exception" from the first element of the array
		# for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		if ($split[0] -eq $exString){
			#create a new ArrayList to remove $split[0]
			$newArray = [System.Collections.ArrayList] $split
			$newArray.Remove($exString)
	
			# update $split and $line
			$split = $newArray
			$line = $newArray -join(" ")
		}
	
		# remove the lines that has less than 7 elements
		if ($split.count -ge 7){
			write-host $line
			$writeStream.WriteLine($line)
		}
	}
	
	# Write to the destination blob
	$writeStream.Flush()
	$memStream.Seek(0, "Begin")
	$destBlob.UploadFromStream($memStream)
	
	#endregion
	
	#region - export a log file from the cluster to the SQL database
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	$tableName_log4j = "log4jlogs"
	
	# Connection string for Azure SQL Database.
	# Comment if using SQL Server
	$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	# Connection string for SQL Server.
	# Uncomment if using SQL Server.
	#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
	$exportDir_log4j = "/tutorials/usesqoop/data"
	
	# Submit a Sqoop job
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
	
	#endregion
	
	#region - export a Hive table
	
	$tableName_mobile = "mobiledata"
	$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion
	
	#region - import a database
	
	$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
	
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0907_2016-->