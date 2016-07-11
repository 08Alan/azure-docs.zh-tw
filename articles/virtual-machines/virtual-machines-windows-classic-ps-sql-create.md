<properties
	pageTitle="以 Azure PowerShell 建立 SQL Server 虛擬機器 (傳統) | Microsoft Azure"
	description="提供使用 SQL Server 虛擬機器資源庫映像建立 Azure VM 的步驟和 PowerShell 指令碼。本主題使用傳統部署模式。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/20/2016"
	ms.author="jroth" />

# 使用 Azure PowerShell 佈建 SQL Server 虛擬機器 (傳統)

## 概觀

這篇文章提供如何使用 PowerShell Cmdlet 在 Azure 中建立 SQL Server 虛擬機器的步驟。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 如果您需要本主題的 Resource Manager 版本，請參閱[使用 Azure PowerShell Resource Manager 佈建 SQL Server 虛擬機器](virtual-machines-windows-ps-sql-create.md)。

## 安裝並設定 PowerShell

1. 如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

2. [安裝最新版的 Azure PowerShell Cmdlet](../powershell-install-configure.md)。

3. 在安裝之後，啟動 Windows PowerShell。

4. 然後，使用 Add-AzureAccount 命令，將 PowerShell 與您的 Azure 訂用帳戶連線。

		Add-AzureAccount

## 決定您的目標 Azure 區域

您的 SQL Server 虛擬機器將託管於存放在特定 Azure 區域的雲端服務。下列步驟可協助您決定將用於本教學課程其餘部分的服務區域、儲存體帳戶和雲端服務。

1. 決定您想要用來託管 SQL Server VM 的資料中心。下列 PowerShell 命令將會顯示可用區域的詳細資料，在結尾附加摘要清單。

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  找到您偏好的位置後，請設定一個名為 **$dcLocation** 的變數至該區域。

		$dcLocation = "<region name>"

## 設定您的訂閱和儲存體帳戶

1. 決定您將用於新虛擬機器的 Azure 訂用帳戶。

		(Get-AzureSubscription).SubscriptionName

1. 將您的目標 Azure 訂用帳戶指派至 **$subscr** 變數。然後將此設為目前的 Azure 訂用帳戶。

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. 然後檢查現有的儲存體帳戶。下列指令碼會顯示存在於您所選擇區域中的所有儲存體帳戶：

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE] 如果您需要新的儲存體帳戶，請先使用 AzureStorageAccount 命令建立全部小寫的儲存體帳戶名稱，如下列範例所示：**New-AzureStorageAccount -StorageAccountName "<儲存體帳戶名稱>" -Location $dcLocation**

1. 將目標儲存體帳戶名稱指派至 **$staccount**。接著，使用 **Set-AzureSubscription** 設定訂用帳戶和目前的儲存體帳戶。

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## 選取 SQL Server 虛擬機器映像

1. 了解 SQL Server 虛擬機器映像庫的可用清單。這些映像都有以「SQL」開頭的 **ImageFamily** 屬性。下列查詢會顯示可供使用且已預先安裝 SQL Server 的映像系列。

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. 當您找到虛擬機器映像系列時，此系列中可能有多個已發行的映像。使用下列指令碼尋找您所選映像系列中最新發佈的虛擬機器映像名稱 (例如 **SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2**)：

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## 建立虛擬機器

最後，使用 PowerShell 建立虛擬機器：

1. 建立雲端服務來裝載新的 VM。請注意，您也可以使用現有的雲端服務。建立新的變數 **$svcname**，並以雲端服務的簡短名稱命名。

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. 指定虛擬機器名稱和大小。如需關於虛擬機器大小的詳細資訊，請參閱 [Azure 的虛擬機器大小](virtual-machines-linux-sizes.md)。

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. 指定本機系統管理員帳戶與密碼。

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. 執行下列指令碼來建立虛擬機器。

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] 如需其他說明及組態選項，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-windows-classic-create-powershell.md)中的**建置命令集**一節。

## PowerShell 指令碼範例

下列指令碼提供可建立 **SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2** 虛擬機器的完整指令碼範例。如果您使用這個指令碼，您必須根據本主題中先前步驟來自訂初始變數。

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5"

	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1


## 使用遠端桌面連線

1. 在目前使用者的文件資料夾中建立 .RDP 檔案以啟動這些虛擬機器並完成安裝：

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. 在文件目錄中，啟動 RDP 檔案。連接先前提供的管理員使用者名稱與密碼 (例如，如果您的使用者名稱是 VMAdmin，請指定「\\VMAdmin」為使用者並提供密碼)。

		.\vm1.rdp

## 完成 SQL Server Machine 的遠端存取組態設定

透過遠端桌面登入該機器之後，根據 [Azure VM 中設定 SQL Server 連線的步驟](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)內的指示設定 SQL Server。

## 後續步驟

您可以在[虛擬機器文件](virtual-machines-windows-classic-create-powershell.md)中找到其他使用 PowerShell 佈建虛擬機器的指示。如需與 SQL Server 和進階儲存體相關的其他指令碼，請參閱[在虛擬機器上搭配使用 Azure 進階儲存體和 SQL Server](virtual-machines-windows-classic-sql-server-premium-storage.md)。

在許多情況下下，下一個步驟是將資料庫移轉到這個新的 SQL Server VM。如需資料庫移轉的指引，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

如果您也想了解如何使用 Azure 入口網站建立 SQL 虛擬機器，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。請注意，本教學課程將逐步說明從入口網站利用建議的資源管理員模型建立 VM，而不是利用本 PowerShell 主題中所使用的傳統模型建立 VM。

除了上述資源，我們也建議您檢閱[在 Azure 虛擬機器中執行 SQL Server 的其他相關主題](virtual-machines-windows-sql-server-iaas-overview.md)。

<!---HONumber=AcomDC_0629_2016-->