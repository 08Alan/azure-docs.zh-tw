---
title: "將 AWS VM 移轉至 Azure | Microsoft Docs"
description: "將 Amazon Web Services (AWS) EC2 執行個體移轉至 Azure 虛擬機器。 此案例使用受控磁碟來簡化您的雲端儲存體。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a6549999003d4b1c8e2b8a8e2a2fafef942bce43
ms.lasthandoff: 04/27/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>從 Amazon Web Services (AWS) 移轉至 Azure 受控磁碟

您可藉由上傳 VHD 將 Amazon Web Services (AWS) EC2 執行個體移轉至 Azure。 如果您想要在 Azure 中從相同映像建立多個 VM，您必須先將 VM 一般化，然後將一般化 VHD 匯出至本機目錄。 上傳 VHD 後，您可以建立新的 Azure VM，其使用[受控磁碟](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)進行儲存。 使用 Azure 受控磁碟就不需要為 Azure IaaS VM 管理儲存體帳戶。 您只需要指定類型 (進階或標準)，還有您需要的磁碟大小，Azure 就會替您建立及管理磁碟。 

開始此程序前，請務必先檢閱[規劃移轉至受控磁碟](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

將任何 VHD 上傳至 Azure 之前，您應該遵循[準備 Windows VHD 或 VHDX 以上傳至 Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="before-you-begin"></a>開始之前
如果您使用 PowerShell，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。


## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 將 Windows VM 一般化

使用 Sysprep 將 VM 一般化，可從 VHD 中移除任何電腦特有資訊和個人帳戶資訊，並準備要作為映像的電腦。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果您是第一次在將 VHD 上傳至 Azure 之前執行 Sysprep，請確定您已[準備好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 再執行 Sysprep。 
> 
> 

1. 登入 Windows 虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。
3. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化] 核取方塊。
4. 在 [關機選項] 中選取 [關機]。
5. 按一下 [確定] 。
   
    ![啟動 Sysprep](./media/aws-to-azure/sysprepgeneral.png)
6. Sysprep 完成時，會關閉虛擬機器。 不要重新啟動 VM。



## <a name="export-the-vhd-from-an-ec2-instance"></a>從 EC2 執行個體匯出 VHD

1.    如果您使用的是 Amazon Web Services (AWS)，請將 EC2 執行個體匯出至 Amazon S3 貯體中的 VHD。 請依照 Amazon 文件中所述的「匯出 Amazon EC2 執行個體」步驟，安裝 Amazon EC2 命令列介面 (CLI) 工具並執行 create-instance-export-task 命令，將 EC2 執行個體匯出到 VHD 檔案。 執行 create-instance-export-task 命令時，請務必讓 DISK_IMAGE_FORMAT 變數使用 VHD。 已匯出的 VHD 檔案會儲存在您在該程序期間所指定的 Amazon S3 貯體中。

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    從 S3 貯體下載 VHD 檔案。 選取 VHD 檔案，然後選取 [動作] > [下載]。



## <a name="log-in-to-azure"></a>登入 Azure
如果尚未安裝 Azure PowerShell 版本，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

1. 開啟 Azure PowerShell，並登入您的 Azure 帳戶。 這會開啟一個可供您輸入 Azure 帳戶認證的快顯視窗。
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 取得您可用訂用帳戶的訂用帳戶識別碼。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用訂用帳戶識別碼來設定正確的訂用帳戶。 使用正確訂用帳戶的識別碼取代 `<subscriptionID>`。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>取得儲存體帳戶
您需要一個 Azure 中的儲存體帳戶來裝載上傳的 VM 映像。 您可以使用現有的儲存體帳戶或建立新帳戶。 

如果您要使用 VHD 為 VM 建立受控磁碟，儲存體帳戶位置與您將建立 VM 的位置必須相同。

若要顯示可用的儲存體帳戶，請輸入︰

```powershell
Get-AzureRmStorageAccount
```

如果您想要使用現有的儲存體帳戶，請移至[上傳 VM 映像](#upload-the-vm-vhd-to-your-storage-account)一節。

如果您需要建立儲存體帳戶，請依照下列步驟操作：

1. 您需要在當中建立儲存體帳戶的資源群組名稱。 若要找出您訂用帳戶中的所有資源群組，請輸入︰
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    若要在**美國中部**區域建立名為 **myResourceGroup** 的資源群組，請輸入︰

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. 使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) Cmdlet，在此資源群組中建立名為 **mystorageaccount**的儲存體帳戶：
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName 的有效值包括：
   
   * **Standard_LRS** - 本地備援儲存體。 
   * **Standard_ZRS** - 區域備援儲存體。
   * **Standard_GRS** - 異地備援儲存體。 
   * **Standard_RAGRS** - 讀取權限異地備援儲存體。 
   * **Premium_LRS** - 進階本地備援儲存體。 

## <a name="upload-the-vhd-to-your-storage-account"></a>將 VHD 上傳至儲存體帳戶

使用 [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) Cmdlet，將 VHD 上傳至儲存體帳戶中的容器。 這個範例會將檔案 **myVHD.vhd** 從 `"C:\Users\Public\Documents\Virtual hard disks\"` 上傳至 **myResourceGroup** 資源群組中名為 **mystorageaccount** 的儲存體帳戶。 檔案會放入名為 **mycontainer** 的容器，新的檔案名稱會是 **myUploadedVHD.vhd**。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，您會得到看起來如以下的回應：

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

視您的網路連線和 VHD 檔案大小而定，此命令可能需要一些時間才能完成

儲存 [目的地 URI] 路徑，以便日後想要使用上傳的 VHD 建立受控磁碟或新的 VM 時使用。

### <a name="other-options-for-uploading-a-vhd"></a>上傳 VHD 的其他選項

您也可以使用下列其中一種方法將 VHD 上傳至儲存體帳戶：

-   [Azure 儲存體複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure 儲存體總管上傳 Blob](https://azurestorageexplorer.codeplex.com/)

-   [儲存體匯入/匯出服務 REST API 參考](https://msdn.microsoft.com/library/dn529096.aspx)

    如果預估的上傳時間長度超過 7 天，我們建議使用匯入/匯出服務。 您可以使用 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) 從資料大小和傳輸單位來評估時間。 

    匯入/匯出可用來複製到標準儲存體帳戶。 您必須使用 AzCopy 之類的工具，從 Standard 儲存體帳戶複製到進階儲存體帳戶。

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>從上傳的 VHD 建立受控映像 

使用一般化 OS VHD 建立受控映像。


1.  首先，設定一般參數：

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  使用一般化 OS VHD 建立映像。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>設定映像的一些變數

首先，我們需要收集映像的基本資訊，並建立映像的變數。 這個範例使用名為 **myImage** 的受控 VM 映像，其位於**美國中西部**位置的 **myResourceGroup** 資源群組中。 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
建立[虛擬網路](../../virtual-network/virtual-networks-overview.md)的 vNet 和子網路。

1. 建立子網路。 這個範例會建立名為 **mySubnet** 且具有位址首碼 **10.0.0.0/24** 的子網路。  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 建立虛擬網路 這個範例會建立名為 **myVnet** 且具有位址首碼 **10.0.0.0/16** 的虛擬網路。  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>建立公用 IP 位址和網路介面

若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP 位址。 此範例會建立名為 **myPip** 的公用 IP 位址。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 建立 NIC。 此範例會建立名為 **myNic** 的 NIC。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則

若要能夠使用 RDP 登入 VM，您必須有可在連接埠 3389 上允許 RDP 存取的網路安全性規則 (NSG)。 

此範例會建立名為 **myNsg** 的 NSG，其包含的規則 **myRdpRule** 可允許透過連接埠 3389 的 RDP 流量。 如需 NSG 的詳細資訊，請參閱[使用 PowerShell 對 Azure 中的 VM 開啟連接埠](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>建立虛擬網路的變數

為已完成的虛擬網路建立變數。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>取得 VM 的認證

下列 Cmdlet 會開啟視窗，讓您輸入新的使用者名稱和密碼，作為從遠端存取 VM 時使用的本機管理員帳戶。 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>設定 VM 名稱、電腦名稱和 VM 大小的變數

1. 建立 VM 名稱和電腦名稱的變數。 此範例將 VM 名稱設定為 **myVM**，將電腦名稱設定為 **myComputer**。

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 設定虛擬機器的大小。 這個範例會建立 **Standard_DS1_v2** 大小的 VM。 如需詳細資訊，請參閱 [VM 大小](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)文件。

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. 將 VM 名稱和大小新增至 VM 設定。

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>將 VM 映像設定為新 VM 的來源映像

使用受控 VM 映像的識別碼設定來源影像。

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>設定 OS 組態並新增 NIC。

輸入儲存體類型 (PremiumLRS 或 StandardLRS) 和 OS 磁碟的大小。 這個範例將帳戶類型設定為 **PremiumLRS**、將磁碟大小設定為 **128GB**，並將磁碟快取設定為 **ReadWrite**。

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>建立 VM

使用我們已建立並儲存在 **$vm** 變數中的組態來建立新 VM。

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>後續步驟

若要登入新的虛擬機器，請瀏覽至[入口網站](https://portal.azure.com)中的 VM，按一下 [連接] 並開啟遠端桌面 RDP 檔案。 使用原始虛擬機器的帳戶認證來登入新的虛擬機器。 如需詳細資訊，請參閱 [如何連接和登入執行 Windows 的 Azure 虛擬機器](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
 

