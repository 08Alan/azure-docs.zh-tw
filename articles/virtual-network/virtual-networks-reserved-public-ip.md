---
title: "使用 PowerShell 管理保留的 IP 位址 (傳統) | Microsoft Docs"
description: "了解保留的 IP 位址 (傳統)，以及如何使用 PowerShell 管理這些 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: 55071ffe1bcc17b2181b4c52f51d28571a2f0eff


---
# <a name="reserved-ip-addresses-classic"></a>保留的 IP 位址 (傳統)

> [!div class="op_single_selector"]
- [Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
- [範本](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (傳統)](virtual-networks-reserved-public-ip.md)

Azure 中的 IP 位址分為兩個類別：動態和保留。 依預設由 Azure 管理的公用 IP 位址是動態的。 這表示當資源時關閉或解除配置時，用於指定雲端服務 (VIP)，或直接存取 VM 或角色執行個體 (ILPIP) 的 IP 位址可以隨時變更。

若要防止 IP 位址變更，您可以保留 IP 位址。 保留的 IP 僅能用作 VIP，即使資源都關閉或解除配置，也能確保雲端服務的 IP 位址將會相同。 此外，您可以轉換現有的動態 IP，作為保留的 IP 位址的 VIP。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 了解如何使用 [Resource Manager 部署模型](virtual-network-ip-addresses-overview-arm.md)來保留靜態公用 IP 位址。

若要深入了解 Azure 中的 IP 位址，請閱讀 [IP 位址](virtual-network-ip-addresses-overview-classic.md)文章。

## <a name="when-do-i-need-a-reserved-ip"></a>何時需要保留的 IP？
* **您想要確保 IP 會保留在您的訂用帳戶中**。 如果您想要保留 IP 位址，使其在任何情況下將不會從訂用帳戶釋放，您應該使用保留的公用 IP。  
* **即使在已停止或解除配置狀態 (VM)，您想要保持 IP 與雲端服務之間的關聯**。 如果您想要讓服務可以使用 IP 位址來存取，且即使雲端服務中的 VM 已停止或解除配置，該 IP 位址也不會變更。
* **您想要確保來自 Azure 的輸出流量使用可預測的 IP 位址**。 您可能必須設定內部部署防火牆，以便僅允許來自特定 IP 位址的流量。 藉由保留 IP，您將會知道來源 IP 位址，且不必因為 IP 變更而更新您的防火牆規則。

## <a name="faq"></a>常見問題集
1. 我可以針對所有 Azure 服務使用保留的 IP 嗎？  
   * 保留的 IP 僅可用於 VM 和雲端服務透過 VIP 公開的執行個體角色。
2. 我可以有多少保留的 IP？  
   * 請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
3. 保留的 IP 是否會收取費用？
   * 請參閱＜ [保留的 IP 位址定價詳細資料](http://go.microsoft.com/fwlink/?LinkID=398482) ＞以取得定價資訊。
4. 我該如何保留 IP 位址？
   * 您可以使用 PowerShell、[Azure 管理 REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) 或 [Azure 入口網站](https://portal.azure.com)，在特定區域中保留 IP 位址。 這個保留的 IP 位址與您的訂用帳戶相關聯。
5. 我可以使用這個搭配以同質群組為基礎的 VNet 嗎？
   * 保留的 IP 僅在區域 VNet 才受支援。 不支援與同質群組相關聯的 VNet。 如需有關將 VNet 與區域或同質群組建立關聯的詳細資訊，請參閱＜ [關於區域 VNet 與同質群組](virtual-networks-migrate-to-regional-vnet.md)＞。

## <a name="manage-reserved-vips"></a>管理保留的 VIP

確保您完成[安裝並設定 PowerShell](/powershell/azureps-cmdlets-docs) 文章中的步驟來安裝和設定 PowerShell。 

您必須將保留的 IP 新增至訂用帳戶才能使用。 若要在 *美國中部* 位置從可用的公用 IP 位址集區來建立保留的 IP，請執行下列命令：

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

但是請注意，您無法指定正在保留的 IP。 若要檢視哪些 IP 位址會保留在訂用帳戶中，執行下列 PowerShell 命令，並注意 *ReservedIPName* 和 *Address* 的值：

```powershell
Get-AzureReservedIP
```

預期的輸出：

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

一旦保留 IP，其就會與您的訂用帳戶相關聯，直到刪除為止。 若要刪除如上所示之保留的 IP，請執行下列 PowerShell 命令：

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>保留現有雲端服務的 IP 位址
您可以新增 `-ServiceName` 參數，以保留現有雲端服務的 IP 位址。 若要在*美國中部*位置保留雲端服務 *TestService* 的 IP 位址，請執行下列 PowerShell 命令：

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>建立保留的 IP 至新雲端服務的關聯
下面的指令碼會建立新保留的 IP，然後將其關聯至新的雲端服務，稱為 *TestService*。

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> 當您建立保留的 IP 以搭配使用雲端服務時，您仍然需要針對輸入通訊使用 *VIP:&lt;連接埠號碼>* 來參照 VM。 保留 IP 並不表示您可以直接連接至 VM。 保留的 IP 會指派給已部署 VM 的雲端服務。 如果您想要透過 IP 直接連接到 VM，您必須設定執行個體層級公用 IP。 執行個體層級公用 IP 是一種公用 IP 類型 (稱為 ILPIP)，其會直接指派給您的 VM。 此類型 IP 無法保留。 請參閱＜ [執行個體層級公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md) ＞以取得詳細資訊。
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>從執行中部署移除保留的 IP
若要針對上述指令碼中建立的新服務，移除新增至其中之保留的 IP，請執行下列 PowerShell 命令：

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> 從執行中部署移除保留的 IP 並不會從您的訂用帳戶移除保留項目。 這僅會釋出 IP，以便訂用帳戶中的其他資源可以使用。
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>建立保留的 IP 至執行中部署的關聯
下列命令會建立稱為 *TestService2* 的新雲端服務，以及稱為 *TestVM2* 的新 VM，然後建立稱為 *MyReservedIP* 之現有保留的 IP 至雲端服務的關聯：

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>使用服務組態檔建立保留的 IP 至雲端服務的關聯
您也可以使用服務組態 (CSCFG) 檔建立保留的 IP 至雲端服務的關聯。 下方範例 XML 示範如何設定雲端服務，以便使用稱為 *MyReservedIP*之保留的 VIP：

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>後續步驟
* 了解 [IP 位址](virtual-network-ip-addresses-overview-classic.md) 在傳統部署模型中的運作方式。
* 深入了解 [保留的私人 IP 位址](virtual-networks-reserved-private-ip.md)。
* 深入了解 [執行個體層級公用 IP (ILPIP) 位址](virtual-networks-instance-level-public-ip.md)。




<!--HONumber=Feb17_HO3-->


