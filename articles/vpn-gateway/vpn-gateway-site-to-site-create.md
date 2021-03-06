---
title: "將內部部署網路連接至 Azure 虛擬網路：站對站 VPN：傳統入口網站 | Microsoft Docs"
description: "透過公用網際網路建立從內部部署網路至 Azure 虛擬網路之 IPsec 連線的步驟。 這些步驟可協助您使用傳統入口網站和傳統部署模型，建立跨單位的站對站 VPN 閘道連線。"
services: vpn-gateway
documentationcenter: 
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 024ecb29-64de-4ff1-84f1-1a45a8595f0b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: d0cedf73aa3f73e672a73b6abaca5eb8c22a76a7
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-classic-portal-classic"></a>使用傳統入口網站建立具有站對站連線的 VNet (傳統)

本文說明如何使用傳統入口網站來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用於傳統部署模型。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [傳統 - Azure 入口網站](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [傳統 - 傳統入口網站](vpn-gateway-site-to-site-create.md)
> 
>

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-site-to-site-create/site-to-site-connection-diagram.png)


站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

#### <a name="additional-configurations"></a>其他組態

如果您想要將 VNet 連接在一起，請參閱 [設定傳統部署模型的 VNet 對 VNet 連線](virtual-networks-configure-vnet-to-vnet-connection.md)。 如果您想要網站間連接新增至已經有連接的 VNet，請參閱[將 S2S 連接新增至已有現有 VPN 閘道連接的 VNet](vpn-gateway-multi-site.md)。
## <a name="before-you-begin"></a>開始之前

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

在開始設定之前，請確認您具備下列項目：

* 相容的 VPN 裝置 (以及能夠進行設定的人員)。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您需要與能夠提供那些詳細資料的人協調。
* 您的 VPN 裝置對外開放的公用 IP 位址。 此 IP 位址不能位於 NAT 後方。
* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。

## <a name="CreateVNet"></a>建立虛擬網路
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 按一下螢幕左下角的 [新增]。 在導覽窗格中依序按一下 [網絡服務] 和 [虛擬網路]。 按一下 [Custom Create]  開始組態精靈。
3. 若要建立 VNet，請在下列頁面上輸入組態設定：

## <a name="Details"></a>虛擬網路詳細資料頁面
輸入以下資訊：

* **名稱**：為虛擬網路命名。 例如， *EastUSVNet*。 當您部署 VM 和 PaaS 執行個體時，將會使用此虛擬網路名稱，因此您可能不會想要太過複雜的名稱。
* **位置**：位置會與您要存放資源 (VM) 的實體位置 (區域) 直接相關。 例如，如果想要您部署到此虛擬網路的 VM 實際上位於 *美國東部*，請選取該位置。 建立關聯之後，您就無法變更與虛擬網路相關聯的區域。

## <a name="DNS"></a>DNS 伺服器和 VPN 連線能力頁面
輸入下列資訊，然後按一下右下角的 [下一步] 箭頭。

* **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從捷徑功能表中選取先前註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器。 它可讓您指定要用於此虛擬網路之名稱解析的 DNS 服務。
* **設定網站間 VPN**：選取 [設定網站間 VPN] 的核取方塊。
* **區域網路**：表示實體內部部署位置的本機網路。 您可以選取先前建立的區域網路，或者可以建立新的區域網路。 不過，如果您選擇使用您先前建立的區域網路，請移至 [區域網路]  組態頁面，並確認 VPN 裝置的 VPN 裝置 IP 位址 (公開 IPv4 位址) 正確無誤。

## <a name="Connectivity"></a>網站間連線能力頁面
如果您正在建立新的區域網路，將看到 [網站間連線能力] 頁面。 如果您想要使用先前建立的區域網路，此頁面不會出現在精靈中，而您可以移至下一節。

輸入下列資訊，然後按 [下一步] 箭頭。

* **名稱**：您想要命名區域 (內部部署) 網站的名稱。
* **VPN 裝置 IP 位址**：您用來連接到 Azure 之內部部署 VPN 裝置的公開 IPv4 位址。 VPN 裝置不能位於 NAT 後方。
* **位址空間**：包含起始 IP 和 CIDR (位址計數)。 您指定想要透過虛擬網路閘道，傳送至本機內部部署位置的位址範圍。 如果目的地 IP 位址落在此處指定的範圍內，將會透過虛擬網路閘道進行路由傳送。
* **加入位址空間**：如果您有多個想要透過虛擬網路閘道傳送的位址範圍，請指定每個額外位址範圍。 稍後您可以在 [區域網路]  頁面上新增或移除範圍。

## <a name="Address"></a>虛擬網路位址空間頁面
指定您想要用於虛擬網路的位址範圍。 這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。

特別重要的是，您選取的範圍不得與用於內部部署網路的任何範圍重疊。 您必須與您的網路管理員協調。 您的網路管理員可能需要從內部部署網路位址空間中切割出 IP 位址範圍，以供您用於虛擬網路。

輸入下列資訊，然後按一下右下角的核取記號來設定您的網路。

* **位址空間**：包括起始 IP 和位址計數。 請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。
* **新增子網路**：包括起始 IP 和位址計數。 不需要其他子網路，但是您可以為將擁有靜態 DIP 的 VM 建立個別的子網路。 或者，您可以讓您的 VM 位於與其他角色執行個體不同的子網路中。
* **新增閘道子網路**：按一下以新增閘道子網路。 閘道器子網路僅用於虛擬網路閘道，而且為這個組態的必要項目。

按一下頁面底部的核取記號，以建立虛擬網路。 完成時，您將在 Azure 傳統入口網站的 [網路] 頁面上看到 [狀態] 下列出 [已建立]。 建立了 VNet 之後，您便可設定虛擬網路閘道。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>設定虛擬網路閘道
設定虛擬網路閘道器來建立安全的網站間連線。 請參閱 [在 Azure 傳統入口網站中設定虛擬網路閘道](vpn-gateway-configure-vpn-gateway-mp.md)。

## <a name="next-steps"></a>後續步驟
 一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。


