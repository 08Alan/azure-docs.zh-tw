---
title: "Azure 虛擬網路和 Windows 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 來管理 Azure 虛擬網路和 Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7015a5317c631dad9079f2694051fa7fb28d232b
ms.lasthandoff: 04/26/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>使用 Azure PowerShell 來管理 Azure 虛擬網路和 Windows 虛擬機器

在本教學課程中，您將了解如何在虛擬網路 (VNet) 中建立多部虛擬機器 (VM)，並設定這些機器間的網路連線。 完成時，將可從網際網路透過連接埠 80 (適用於 HTTP 連線) 存取「前端」VM。 具有 SQL Server 資料庫的「後端」VM 將被隔離，而只有從前端 VM 透過連接埠 1433 才能存取它。

您可以使用最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 模組來完成本教學課程中的步驟。

## <a name="create-vnet"></a>建立 VNet

VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 在 VNet 內，您可以找到子網路、可供連線到這些子網路的規則，以及從 VM 到子網路的連線。

您必須先使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0) 來建立資源群組，才能建立任何其他 Azure 資源。 下列範例會在 `westus` 位置建立名為 `myRGNetwork` 的資源群組：

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。 NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

使用 [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0) 來建立子網路：

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

使用 [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork?view=azurermps-3.8.0) 來建立使用 `myFrontendSubnet` 且名為 `myVNet` 的 VNET：

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>建立前端 VM

若要讓 VM 在 VNet 中進行通訊，它需要一個虛擬網路介面 (NIC)。 存取 `myFrontendVM` 時是從網際網路存取，因此它也需要一個公用 IP 位址。 

使用 [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress?view=azurermps-3.8.0) 來建立公用 IP 位址：

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

使用 [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-3.8.0) 來建立 NIC：


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼：

```powershell
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-3.8.0)、[Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem?view=azurermps-3.8.0)、[Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage?view=azurermps-3.8.0)、[Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-3.8.0)、[Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?view=azurermps-3.8.0) 和 [New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-3.8.0) 來建立 VM。 

```powershell
$frontendVM = New-AzureRmVMConfig -VMName myFrontendVM -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem -VM $frontendVM -Windows -ComputerName myFrontendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage -VM $frontendVM -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$frontendVM = Set-AzureRmVMOSDisk -VM $frontendVM -Name myFrontendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface -VM $frontendVM -Id $frontendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $frontendVM
```

## <a name="install-web-server"></a>安裝 Web 伺服器

您可以使用遠端桌面工作階段在 `myFrontendVM` 上安裝 IIS。 您必須取得 VM 的公用 IP 位址才能存取它。

您可以使用 [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-3.8.0) 來取得 `myFrontendVM` 的公用 IP 位址。 下列範例會取得稍早建立之 `myPublicIPAddress` 的 IP 位址︰

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGNetwork -Name myPublicIPAddress | select IpAddress
```

請記下此 IP 位址，以便在未來的步驟中使用它。

使用下列命令來建立與 `myFrontendVM` 的遠端桌面工作階段。 以您先前記錄下來的位址取代 `<publicIPAddress>`。 出現提示時，請輸入您在建立 VM 時所使用的認證。

```
mstsc /v:<publicIpAddress>
``` 

您現在已登入 `myFrontendVM`，可以使用一行 PowerShell 來安裝 IIS，並啟用本機防火牆規則來允許 Web 流量通過。 開啟 PowerShell 提示字元並執行下列命令：

使用 [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) 來執行會安裝 IIS Web 伺服器的自訂指令碼擴充功能：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

現在您可以使用公用 IP 位址來瀏覽至 VM，以查看 IIS 網站。

![IIS 預設網站](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>管理內部網路流量

網路安全性群組 (NSG) 包含安全性規則清單，這些規則可允許或拒絕傳送到已連線至 VNet 之資源的網路流量。 NSG 可與子網路或連接到 VM 的個別 NIC 建立關聯。 您可以使用 NSG 規則來透過連接埠開啟或關閉對 VM 的存取。 建立 `myFrontendVM` 時，會自動開啟輸入連接埠 3389 以供 RDP 連線使用。

您可以使用 NSG 來設定 VM 的內部通訊。 在本節中，您將了解如何在網路中建立額外的子網路並將 NSG 指派給它，以允許透過連接埠 1433 從 `myFrontendVM` 連線到 `myBackendVM`。 此子網路會接著在 VM 建立後會指派給 VM。

您可以為後端子網路建立 NSG，以僅允許從 `myFrontendVM` 傳送內部流量給 `myBackendVM`。 下列範例會使用 [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig?view=azurermps-3.8.0) 來建立名為 `myBackendNSGRule` 的 NSG 規則︰

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

使用 [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup?view=azurermps-3.8.0) 來建立名為 `myBackendNSG` 的新網路安全性群組：

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>新增後端子網路

使用 [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0) 將 `myBackEndSubnet` 新增到 `myVNet`：

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myRGNetwork -Name myVNet
```

## <a name="create-back-end-vm"></a>建立後端 VM

建立後端 VM 的最簡單方式是使用 SQL Server 映像。 本教學課程只會建立具有資料庫伺服器的 VM，而不會提供有關存取該資料庫的資訊。

建立 `myBackendNic`：

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼：

```powershell
$cred = Get-Credential
```

建立 `myBackendVM`：

```powershell
$backendVM = New-AzureRmVMConfig -VMName myBackendVM -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem -VM $backendVM -Windows -ComputerName myBackendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage -VM $backendVM -PublisherName MicrosoftSQLServer -Offer SQL2016-WS2016 -Skus Enterprise -Version latest
$backendVM = Set-AzureRmVMOSDisk -VM $backendVM -Name myBackendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface -VM $backendVM -Id $backendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $backendVM
```

所使用的映像已安裝 SQL Server，但在本教學課程中並不使用。 包含它是為了示範如何設定一個 VM 來處理 Web 流量、一個 VM 來處理資料庫管理。


