---
title: "使用 Azure CLI 2.0 針對 Linux VM 開啟連接埠 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 部署模型和 Azure CLI 2.0 對 Linux VM 開啟連接埠/建立端點"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: f1b4910ed0f63fd4914113e83e9fe4b483147c3a
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>使用 Azure CLI 針對 Linux VM 開啟連接埠和端點
您可以透過在子網路或 VM 網路介面上建立網路篩選，對 Azure 中的虛擬機器 (VM) 開啟連接埠或建立端點。 您可將控制輸入和輸出流量的這些篩選器放在可接收流量的資源所附加的網路安全性群組上。 讓我們使用連接埠 80 上的 Web 流量的常見範例。 這篇文章說明如何使用 Azure CLI 2.0 來開啟連接埠至 VM。 您也可以使用 [Azure CLI 1.0](nsg-quickstart-nodejs.md) 來執行這些步驟。


## <a name="quick-commands"></a>快速命令
若要建立網路安全性群組和規則，您需要安裝 [Azure CLI 2.0](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 登入 Azure 帳戶。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包括 myResourceGroup、myNetworkSecurityGroup 和 myVnet。

使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例會在 eastus 位置中建立名為 myNetworkSecurityGroup 的網路安全性群組：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create)新增規則以允許流向您 Web 伺服器的 HTTP 流量 (或針對自己的案例 (例如 SSH 存取或資料庫連接) 進行調整)。 下列範例會建立名為 myNetworkSecurityGroupRule 的規則以允許連接埠 80 上的 TCP 流量︰

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

使用 [az network nic update](/cli/azure/network/nic#update)將「網路安全性群組」與 VM 的網路介面 (NIC) 建立關聯。 下列範例將名為 myNic 的現有 NIC 與名為 myNetworkSecurityGroup 的網路安全性群組建立關聯：

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

或者，您也可以使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)將「網路安全性群組」與虛擬網路的子網路建立關聯，而不是只與單一 VM 上的網路介面建立關聯。 下列範例將 myVnet 虛擬網路中名為 mySubnet 的現有子網路，與名為 myNetworkSecurityGroup 的網路安全性群組建立關聯：

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
這裡的快速命令可讓您使流向您 VM 的流量開始正常運作。 「網路安全性群組」提供許多絕佳的功能和細微性來控制對您資源的存取。 您可以深入了解 [建立網路安全性群組和 ACL 規則](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)。

您可以在 Azure Resource Manager 範本中定義網路安全性群組和 ACL 規則。 深入了解 [使用範本建立網路安全性群組](../../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您需要使用連接埠轉送來將唯一的外部連接埠對應至您 VM 上的內部連接埠，請使用負載平衡器和「網路位址轉譯」(NAT) 規則。 例如，您可能會想要對外公開 TCP 連接埠 8080，然後讓流量導向到 VM 上的 TCP 連接埠 80。 您可以深入了解 [建立網際網路面向的負載平衡器](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您建立了簡單的規則來允許 HTTP 流量。 您可以從下列文章中，找到有關建立更詳細環境的資訊︰

* [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)
* [什麼是網路安全性群組 (NSG)？](../../virtual-network/virtual-networks-nsg.md)
* [負載平衡器的 Azure Resource Manager 概觀](../../load-balancer/load-balancer-arm.md)


