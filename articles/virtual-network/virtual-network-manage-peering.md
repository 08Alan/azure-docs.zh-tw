---
title: "建立、變更或刪除 Azure 虛擬網路對等互連 | Microsoft Docs"
description: "了解如何建立、變更或刪除虛擬網路對等互連。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 1e524f13a70b1d08b064d9ff70db66b2b509abe3
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017


---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>建立、變更或刪除虛擬網路對等互連

了解如何建立、變更或刪除虛擬網路對等互連。 虛擬網路對等互連可讓您透過 Azure 骨幹網路來連線同一 Azure 位置中的兩個虛擬網路。 在對等互連建立好之後，系統仍會將這兩個虛擬網路視為不同資源來進行管理。 連線至任一虛擬網路的資源在通訊時會有相同的延遲和頻寬，彷彿資源是連線到相同的虛擬網路。 如果您不熟悉虛擬網路對等互連，建議您閱讀[虛擬網路對等互連概觀](virtual-network-peering-overview.md)一文，並先完成[建立虛擬網路對等互連教學課程](virtual-network-create-peering.md)，再完成本文中的工作。

## <a name="before"></a>開始之前

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您不熟悉 Azure 中的虛擬網路對等互連，建議您先完成[建立虛擬網路對等互連](virtual-network-create-peering.md)教學課程，再閱讀本文。
- 檢閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，以了解對等互連的限制。
- 使用 Azure 帳戶來登入 Azure 入口網站、Azure 命令列介面 (CLI) 或 Azure PowerShell。 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果您使用 PowerShell 命令來完成本文中的工作，請[安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure PowerShell Cmdlet。 若要取得 PowerShell 命令的說明 (包含範例)，請輸入 `get-help <command> -full`。
- 如果您使用 Azure 命令列介面 (CLI) 命令來完成本文中的工作，請[安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 請確定您已安裝最新版的 Azure CLI。 若要取得 CLI 命令的說明，請輸入 `az <command> --help`。

## <a name="about-peering"></a>關於對等互連 

在為虛擬網路建立對等互連之前，請先考慮下列條件約束：
- 要建立對等互連的虛擬網路必須有非重疊的 IP 位址空間。 
- 一旦虛擬網路與另一個虛擬網路對等互連，您便無法在虛擬網路中新增或刪除位址空間。 若要新增或移除位址空間，請刪除對等互連，新增或移除位址空間，然後重新建立對等互連。 若要在虛擬網路中新增或移除位址空間，請閱讀[建立、變更或刪除虛擬網路](virtual-network-manage-network.md#add-address-spaces)一文。 
- 您可以將透過 Resource Manager 所部署的兩個虛擬網路對等互連，或將透過 Resource Manager 所部署的虛擬網路與透過傳統部署模型所部署的虛擬網路對等互連。 您無法將兩個透過傳統部署模型所建立的虛擬網路對等互連。 如果您不熟悉 Azure 部署模型，請閱讀[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
- 將兩個透過 Resource Manager 所建立的虛擬網路對等互連時，您必須為對等互連中的每個虛擬網路設定對等互連。 當您從第一個虛擬網路對第二個虛擬網路建立對等互連時，對等互連狀態是 [已起始]。  當您從第二個虛擬網路對第一個虛擬網路建立對等互連時，其對等互連狀態是 [已連線]。 如果您檢視第一個虛擬網路的對等互連狀態，您會看到其狀態從 [已起始] 變更為 [已連線]。 在兩個虛擬網路對等互連的對等互連狀態變為 [已連線] 之後，您才能成功建立對等互連。 
- 在將透過 Resource Manager 所建立的虛擬網路與透過傳統部署模型所建立的虛擬網路對等互連時，您只會對透過 Resource Manager 所部署的虛擬網路設定對等互連。 您無法對虛擬網路 (傳統) 設定對等互連，也無法在兩個透過傳統部署模型所部署的虛擬網路之間設定對等互連。 當您從虛擬網路 (Resource Manager) 對虛擬網路 (傳統) 建立對等互連時，對等互連狀態先是 [更新中]，然後很快就會變為 [已連線]。   
- 兩個虛擬網路之間建立了對等互連。 對等互連不具轉移性。 如果您在下列項目之間建立對等互連：
    - 虛擬網路 1 和虛擬網路 2
    - 虛擬網路 2 和虛擬網路 3

  則虛擬網路 1 與虛擬網路 3 之間並無法透過虛擬網路 2 而建立對等互連。 如果您想要在虛擬網路 1 與虛擬網路 3 之間設定對等互連，則必須在虛擬網路 1 與虛擬網路 3 之間建立對等互連。
- 您無法使用預設的 Azure 名稱解析在對等互連的虛擬網路中解析名稱。 若要在其他虛擬網路中解析名稱，您必須使用自訂 DNS 伺服器。 若要了解如何設定自有 DNS 伺服器，請閱讀[使用專屬 DNS 伺服器的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)一文。
- 對等互連的兩個虛擬網路中的資源可彼此通訊，且通訊時會有相同的頻寬和延遲，彷彿這些資源是位於相同的虛擬網路中。 不過，每個虛擬機器大小各有其網路頻寬上限。 若要深入了解不同虛擬機器大小的網路頻寬上限，請閱讀 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小文章。
- 您可以將透過 Resource Manager 所部署的虛擬網路 (不論位於相同或不同訂用帳戶中) 對等互連。
- 您可以將透過不同部署模型所部署的虛擬網路 (且位於相同訂用帳戶中) 對等互連。 將透過不同部署模型所部署的虛擬網路 (且位於不同訂用帳戶中) 對等互連的功能目前還是預覽版本。
- 兩個虛擬網路所在的訂用帳戶必須與相同的 Azure Active Directory 租用戶相關聯。 如果您還沒有 AD 租用戶，您可以快速地[建立一個](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)。 
- <a name="roles-permissions"></a>您的帳戶必須具有必要的角色或權限才能建立對等互連。 例如，如果您要將兩個虛擬網路 (分別名為 Vnet1 和 VNet2) 對等互連，您的帳戶必須針對每個虛擬網路獲得下列最基本的角色或權限：
    
    |虛擬網路|部署模型|角色|權限|
    |---|---|---|---|
    |VNet1|資源管理員|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
    | |傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
    |VNet2|資源管理員|[網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
    ||傳統|[傳統網路參與者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

  深入了解[內建角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何對[自訂角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)指派特定權限 (僅限 Resource Manager)。
- 虛擬網路可以既與另一個虛擬網路對等互連，又同時連線到另一個具有 Azure 虛擬網路閘道的虛擬網路。 當虛擬網路同時透過對等互連和閘道進行連線時，虛擬網路之間的流量會透過對等互連組態來流動，而不會透過閘道。
- 我們會針對使用虛擬網路對等互連的輸入和輸出流量收取少許費用。 如需詳細資訊，請參閱 [價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="create-peering"></a>建立對等互連

>[!NOTE]
>想要成功建立對等互連，您必須謹記幾點需求、條件約束和考量事項。 在建立對等互連之前，請先確定您已熟悉本文[關於對等互連](#about-peering)一節中的考量事項清單。
>

1. 使用具有必要[角色和權限](#roles-permissions)的帳戶登入[入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。 如果清單中出現 [虛擬網路 (傳統)] 選項，請勿選取此選項，因為您並無法從透過傳統部署模型所部署的虛擬網路建立對等互連。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要為其建立對等互連的虛擬網路。
4. 在針對所選虛擬網路所出現的窗格中，按一下 [設定] 區段中的 [對等互連]。
5. 按一下 [+ 新增]。 
6. <a name="add-peering"></a>在 [新增對等互連] 刀鋒視窗中，輸入或選取下列設定的值：
    - **名稱︰**對等互連的名稱必須是虛擬網路中的唯一名稱。
    - **虛擬網路部署模型：**選取您想要對等互連之虛擬網路是透過哪個部署模型所部署的。
    - **我知道資源識別碼：**如果您有權讀取所要對等互連的虛擬網路，請讓此核取方塊保持未選取狀態。 如果您無權讀取所要對等互連的虛擬網路或訂用帳戶，請核取此方塊。 針對在核取方塊時所出現的 [資源識別碼] 方塊，輸入您想要對等互連之虛擬網路的完整資源識別碼。 所輸入的資源識別碼必須屬於和此虛擬網路位於相同 Azure [位置](https://azure.microsoft.com/regions)的虛擬網路。 完整資源識別碼的格式類似 /subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>。 您可以檢視虛擬網路的屬性，以取得虛擬網路的資源識別碼。 若要了解如何檢視虛擬網路的屬性，請閱讀[建立、變更或刪除虛擬網路](virtual-network-manage-network.md#view-vnet)一文。
    - **訂用帳戶：**選取您想要對等互連之虛擬網路的[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 視帳戶有權讀取的訂用帳戶而定，系統會列出一或多個訂用帳戶。 如果您已核取 [資源識別碼] 核取方塊，則無法使用這項設定。 若要讓不同訂用帳戶中的兩個虛擬網路對等互連，則這兩個虛擬網路都必須是透過 Resource Manager 所建立的才行。 將透過不同部署模型所建立的訂用帳戶進行跨區對等互連的功能目前還是預覽版本。 請先註冊預覽版本，再於透過不同部署模型所部署的虛擬網路 (且位於不同訂用帳戶中) 之間建立對等互連。 深入了解如何註冊預覽版本，以及如何[讓透過不同部署模型所建立的虛擬網路 (且位於不同訂用帳戶中) 對等互連](virtual-network-create-peering.md#different-subscriptions-different-deployment-models)。
    - **虛擬網路：**選取您想要對等互連的虛擬網路。 您可以選取透過任一 Azure 部署模型所建立的虛擬網路，但是此虛擬網路必須和您用來作為對等互連起始點的虛擬網路位於相同位置。 您必須有權讀取虛擬網路，該虛擬網路才會顯示在清單中。 如果虛擬網路雖有列出卻呈現灰色，原因可能是該虛擬網路的位址空間與此虛擬網路的位址空間重疊。 如果虛擬網路的位址空間重疊，您就無法讓這些位址空間對等互連。 如果您已核取 [資源識別碼] 核取方塊，則無法使用這項設定。
    - **允許虛擬網路存取：**如果您想要讓兩個虛擬網路能夠彼此通訊，請選取 [啟用] (預設值)。 讓虛擬網路能夠彼此通訊，會讓連線到任一虛擬網路的資源能夠以相同的頻寬和延遲彼此通訊，彷彿這些資源是連線到相同的虛擬網路。 兩個虛擬網路中的資源之間所進行的所有通訊都是透過 Azure 私人網路來完成。 網路安全性群組的 **VirtualNetwork** 預設標記包含虛擬網路和對等互連的虛擬網路。 若要深入了解網路安全性群組的預設標記，請閱讀[網路安全性群組概觀](virtual-networks-nsg.md#default-tags)一文。  如果您不想讓流量流到對等互連的虛擬網路，請選取 [停用]。 如果您已讓某個虛擬網路與另一個虛擬網路對等互連，但偶爾想要停用這兩個虛擬網路之間的流量流動，您可以選取 [停用]。 您可能會發現啟用/停用功能比起先刪除再重新建立對等互連更為方便。 此設定停用時，對等互連的虛擬網路之間不會有流量流動。
    - **允許轉送的流量：**核取此方塊可允許轉送到對等互連虛擬網路的流量 (不是源自對等互連虛擬網路的流量) 流到此虛擬網路。 當您在想要對等互連的虛擬網路部署了網路虛擬設備，並建立了使用者定義的路由來透過網路虛擬設備轉送流量時，系統將會經常轉送流量。 如果您讓此方塊保持未核取狀態 (此為預設值)，從對等互連虛擬網路轉送的流量將無法流到此虛擬網路。 啟用這項功能雖能允許轉送的流量通過對等互連，卻不會建立任何使用者定義的路由或網路虛擬設備。 使用者定義的路由和網路虛擬設備是另外建立的。 了解[使用者定義的路由](virtual-networks-udr-overview.md)。
    - **允許閘道傳輸：**如果您有連結到此虛擬網路的虛擬網路閘道，且想要讓來自對等互連虛擬網路的流量能夠流經閘道，請核取此方塊。 例如，此虛擬網路可能會透過虛擬網路閘道連結到內部部署網路。 核取此方塊可讓來自對等互連虛擬網路的流量流經連結到此虛擬網路的閘道。 如果您核取此方塊，對等互連的虛擬網路將無法設定閘道。 在設定從其他虛擬網路到這個虛擬網路的對等互連時，對等互連的虛擬網路必須核取 [使用遠端閘道] 核取方塊。 如果您讓此方塊保持未核取狀態 (此為預設值)，來自對等互連虛擬網路的流量仍會流到此虛擬網路，但無法流經連結到此虛擬網路的虛擬網路閘道。 深入了解[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#site-to-site-and-multi-site-ipsecike-vpn-tunnel)。 
    
    如果您要讓虛擬網路 (Resource Manager) 與虛擬網路 (傳統) 對等互連，您將無法啟用此選項。 雖然流量可在兩個虛擬網路之間流動，但虛擬網路 (傳統) 的流量無法流經連結到虛擬網路 (Resource Manager) 的網路閘道。
    - **使用遠端閘道：**核取此方塊可讓來自此虛擬網路的流量能夠流經連結到所要對等互連之虛擬網路的虛擬網路閘道。 例如，您要對等互連的虛擬網路已連結 VPN 閘道，而能夠與內部部署網路通訊。  核取此方塊將可讓來自此虛擬網路的流量流經連結到對等互連虛擬網路的 VPN 閘道。 如果您核取此方塊，對等互連的虛擬網路必須有連結的虛擬網路閘道，而且必須已核取 [允許閘道傳輸] 核取方塊。 如果您讓此方塊保持未核取狀態 (此為預設值)，來自對等互連虛擬網路的流量仍可流到此虛擬網路，但無法流經連結到此虛擬網路的虛擬網路閘道。 
    
    如果您要讓虛擬網路 (Resource Manager) 與虛擬網路 (傳統) 對等互連，您將無法啟用此選項。 雖然流量可在兩個虛擬網路之間流動，但虛擬網路 (Resource Manager) 的流量無法流經連結到虛擬網路 (傳統) 的網路閘道。
7. 按一下 [確定] 按鈕以在所選虛擬網路中新增子網路。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet peering create](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>檢視或變更對等互連設定

1. 使用具有必要[角色和權限](#roles-permissions)的帳戶登入[入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要為其建立對等互連的虛擬網路。
4. 在針對所選虛擬網路所出現的窗格中，按一下 [設定] 區段中的 [對等互連]。
5. 按一下您想要檢視或變更設定的對等互連。
6. 變更適當的設定。 請閱讀本文＜建立對等互連＞一節之[步驟 6](#add-peering)中各項。 

    >[!NOTE]
    >想要成功建立對等互連，您必須謹記幾點需求、條件約束和考量事項。 在建立對等互連之前，請先確定您已熟悉本文[關於對等互連](#about-peering)一節中的考量事項清單。
    >

7. 按一下 [儲存] 。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet peering list](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list) 可列出虛擬網路的對等互連，[az network vnet peering show](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show) 可顯示特定對等互連的設定，而 [az network vnet peering update](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update) 則可變更對等互連設定。|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) 可擷取檢視對等互連設定，[Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) 則可變更設定。|

## <a name="delete-subnet"></a>刪除對等互連
若您刪除對等互連，來自虛擬網路的流量將不會再流到對等互連的虛擬網路。 當透過 Resource Manager 所部署的虛擬網路建立了對等互連時，每個虛擬網路都會與其他虛擬網路對等互連。 從某個虛擬網路刪除對等互連時雖會停用虛擬網路之間的通訊功能，卻不會從其他虛擬網路刪除對等互連。 位於其他虛擬網路之對等互連的對等互連狀態為 [已中斷連線]。 在重新建立第一個虛擬網路中的對等互連，且兩個虛擬網路的對等互連狀態都變為 [已連線] 之前，您將無法重新建立對等互連。 

如果您想要讓虛擬網路在某些時候進行通訊，卻又不想永遠啟用此通訊功能，您不必刪除對等互連，相反地，您可以將 [允許虛擬網路存取] 設定設為 [停用]。 若要了解如何操作，請閱讀本文[建立對等互連](#create-peering)一節的步驟 6。 您可能會發現停用和啟用網路存取比起先刪除再重新建立對等互連更為簡單。

1. 使用具有必要[角色和權限](#roles-permissions)的帳戶登入[入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時，按一下該項目。
3. 在出現的 [虛擬網路] 刀鋒視窗中，按一下您想要從中刪除對等互連的虛擬網路。
4. 在針對所選虛擬網路所出現的刀鋒視窗中，按一下 [設定] 底下的 [對等互連]。
5. 在出現於 [對等互連] 刀鋒視窗的對等互連清單中，以滑鼠右鍵按一下您想要刪除的對等互連，按一下 [刪除]，然後按 [是] 以從第一個虛擬網路刪除對等互連。
6. 完成上述步驟即可從對等互連中的其他虛擬網路刪除對等互連。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet peering delete](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>接續步驟

- 完成[虛擬網路對等互連教學課程](virtual-network-create-peering.md)
- 建立中樞和輪輻網路拓撲 |[多個](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)|




