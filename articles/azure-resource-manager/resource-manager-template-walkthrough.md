---
title: "Resource Manager 範本逐步解說 | Microsoft Docs"
description: "佈建基本 Azure IaaS 架構的 Resource Manager 範本逐步解說。"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: f1cfd704-f6e1-47d5-8094-b439c279c13f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: navale;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 40687046c832ca39e460f1d7f55f7c188b6242b3
ms.openlocfilehash: 0479a970e2c3b8abaa9305de37a79e2b4128932e


---
# <a name="resource-manager-template-walkthrough"></a>Resource Manager 範本逐步解說
建立範本時的第一個問題是「如何開始？」。 您可以從空白範本著手，遵循[撰寫範本](resource-group-authoring-templates.md#template-format)一文中所述的基本結構，並新增資源和適當的參數和變數。 理想的替代方法會是從瀏覽[快速入門資源庫](https://github.com/Azure/azure-quickstart-templates)著手，並尋找類似您嘗試建立的案例。 您可以合併數個範本或編輯現有的範本，以符合您自己的特定案例。 

讓我們來看一下常見基礎結構：

* 兩部虛擬機器，它們使用相同的儲存體帳戶、位於相同的可用性設定組中，且位於同一個虛擬網路的子網路上。
* 適用於每部虛擬機器的單一 NIC 和 VM IP 位址。
* 連接埠 80 上具有負載平衡規則的負載平衡器

![架構](./media/resource-manager-template-walkthrough/arm_arch.png)

本主題將逐步引導您完成針對該基礎結構建立 Resource Manager 範本的步驟。 您建立的最後一個範本是以名為 [負載平衡器和負載平衡規則中的 2 部 VM](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/)的「快速入門」範本為基礎。

但是，一次要建置很多項目，讓我們先建立儲存體帳戶並加以部署。 在您已熟悉如何建立儲存體帳戶之後，您將新增其他資源並重新部署範本來完成基礎結構。

> [!NOTE]
> 您可以在建立範本時，使用任何類型的編輯器。 Visual Studio 提供了可簡化範本開發的工具，但您不需要 Visual Studio 即可完成本教學課程。 如需有關使用 Visual Studio 建立 Web 應用程式和 SQL Database 部署的教學課程，請參閱 [透過 Visual Studio 建立和部署 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 
> 
> 

## <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本
此範本是 JSON 檔案，可定義您將部署的所有資源。 此外，還允許您定義在部署期間指定的參數、從其他值和運算式建構而來的變數，以及部署的輸出。 

我們從最簡單的範本開始討論：

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

將此檔案儲存為 **azuredeploy.json** (請注意，範本可以有任何您想要的名稱，只是它必須是 json 檔案)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶
在 **resources** 區段內，新增可定義儲存體帳戶的物件，如下所示。 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

您可能想知道這些屬性和值來自何處。 **type**、**name**、**apiVersion** 和 **location** 屬性都是可供所有資源類型使用的標準元素。 您可以在 [資源](resource-group-authoring-templates.md#resources)了解通用元素。 [name] 會設為您在部署期間傳入的參數值，而 [location] 會設為資源群組所使用的位置。 我們將在下面各節探討如何決定 [type] 和 [apiVersion] 。

**properties** 區段包含特定資源類型獨有的所有屬性。 您在此區段中指定的值完全符合 REST API 中可供建立該資源類型的 PUT 作業。 建立儲存體帳戶時，您必須提供 **accountType**。 請注意，在 [可供建立儲存體帳戶的 REST API](https://msdn.microsoft.com/library/azure/mt163564.aspx) 中，REST 作業的 properties 區段也包含 **accountType** 屬性，而且會記載允許的值。 在此範例中，帳戶類型會設定為 **Standard_LRS**，但您可以指定其他值或允許使用者傳入帳戶類型做為參數。

現在讓我們跳回 **parameters** 區段，並查看如何定義儲存體帳戶的名稱。 您可以在 [參數](resource-group-authoring-templates.md#parameters)深入了解如何使用參數。 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
您會在這裡定義字串類型的參數，以保留儲存體帳戶的名稱。 在範本部署期間，將會提供這個參數的值。

## <a name="deploying-the-template"></a>部署範本
我們有可供建立新儲存體帳戶的完整範本。 您應該還記得，範本會儲存在 **azuredeploy.json** 檔案中︰

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

如您在 [資源部署](resource-group-template-deploy.md)一文所見，有很多方式可部署範本。 若要使用 Azure PowerShell 部署範本，請使用：

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile azuredeploy.json
```

弱者，若要使用 Azure CLI 部署範本，請使用：

```azurecli
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

您現在已是儲存體帳戶的擁有者！

後續步驟將會新增部署本教學課程開頭所述架構所需的全部資源。 您會在您一直使用的相同範本中新增這些資源。

## <a name="availability-set"></a>可用性設定組
在儲存體帳戶的定義之後，新增虛擬機器的可用性設定組。 在此情況下，不需要其他屬性，因此其定義相當簡單。 如果您想要定義更新網域計數和容錯網域計數值，請參閱 [可供建立可用性設定組的 REST API](https://msdn.microsoft.com/library/azure/mt163607.aspx) 中的完整 properties 區段。

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

請注意，[name]  已設為某個變數的值。 在此範本中，在幾個不同的地方需要可用性設定組的名稱。 定義該值一次並在多個位置使用它，即可更加輕鬆地維護您的範本。

您為 [type] 指定的值同時包含資源提供者和資源類型。 在可用性設定組中，資源提供者為 **Microsoft.Compute**，而資源類型為 **availabilitySets**。 執行下列 PowerShell 命令，即可取得可用的資源提供者清單：

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

或者，如果您使用 Azure CLI，可以執行下列命令：

```azurecli
    azure provider list
```
在本主題中，假設您要建立儲存體帳戶、虛擬機器和虛擬網路，您將使用︰

* Microsoft.Storage
* Microsoft.Compute
* Microsoft.Network

若要查看特定提供者的資源類型，請執行下列 PowerShell 命令︰

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

或者，在 Azure CLI 中，下列命令將以 JSON 格式傳回可用的類型，並將它儲存到檔案。

```azurecli
    azure provider show Microsoft.Compute --json > c:\temp.json
```

您應會看到 **availabilitySets** 成為 **Microsoft.Compute** 內的其中一個類型。 類型的完整名稱為 **Microsoft.Compute/availabilitySets**。 您可以在範本中決定任何資源的資源類型名稱。

## <a name="public-ip"></a>公用 IP
定義公用 IP 位址。 同樣地，查看 [公用 IP 位址的 REST API](https://msdn.microsoft.com/library/azure/mt163590.aspx) 中要設定的屬性。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

配置方法會設為 [動態]  ，但您可以將它設定為您需要的值或將它設定成接受參數值。 您已讓範本的使用者能夠傳入網域名稱標籤的值。

現在，讓我們看看如何決定 **apiVersion**。 您指定的值僅符合您在建立資源時所要使用的 REST API 版本。 因此，您可以查看該資源類型的 REST API 文件。 或者，您可以對特定類型執行下列 PowerShell 命令。

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
它會傳回下列值︰

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

若要查看使用 Azure CLI 的 API 版本，請執行先前所示的相同 **azure provider show** 命令。

建立新範本時，請挑選最新的 API 版本。

## <a name="virtual-network-and-subnet"></a>虛擬網路和子網路
建立具有一個子網路的虛擬網路。 查看 [虛擬網路的 REST API](https://msdn.microsoft.com/library/azure/mt163661.aspx) 中要設定的所有屬性。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>負載平衡器
您現在將建立一個外部面向的負載平衡器。 因為此負載平衡器使用公用 IP 位址，所以您必須在 **dependsOn** 區段中宣告公用 IP 位址的相依性。 這表示在公用 IP 位址完成部署後，才會部署負載平衡器。 若未定義此相依性，您會收到錯誤，因為 Resource Manager 會嘗試以平行方式部署資源，而且會嘗試將負載平衡器設定為尚未存在的公用 IP 位址。 

您也會在此資源定義中建立後端位址集區、一些要 RDP 到 VM 中的輸入 NAT 規則，以及一個負載平衡規則 (連接埠 80 上的 TCP 探查)。 查看 [負載平衡器的 REST API](https://msdn.microsoft.com/library/azure/mt163574.aspx) 中的所有屬性。

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>網路介面
您將建立 2 個網路介面，每個 VM 各用一個。 您可以使用 [copyIndex() 函式](resource-group-create-multiple.md)來反覆運算複製迴圈 (稱為 nicLoop)，並建立如 `numberOfInstances` 變數中定義的網路介面個數，而不必包含重複的網路介面項目。 網路介面取決於虛擬網路和負載平衡器的建立。 它會使用建立虛擬網路時所定義的子網路，以及負載平衡器識別碼來設定負載平衡器位址集區和輸入 NAT 規則。
看看 [網路介面的 REST API](https://msdn.microsoft.com/library/azure/mt163668.aspx) 中的所有屬性。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>虛擬機器
如同在建立 [網路介面](#network-interface)時一樣，您將使用 copyIndex() 函數建立 2 部虛擬機器。
VM 的建立取決於儲存體帳戶、網路介面和可用性設定組。 如 `storageProfile` 屬性所定義，將從 Marketplace 映像建立此 VM - `imageReference` 用來定義映像發行者、優惠、sku 和版本。 最後，診斷設定檔已設定成啟用 VM 的診斷功能。 

若要尋找 Marketplace 映像的相關屬性，請依照[選取 Linux 虛擬機器映像](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[選取 Windows 虛擬機器映像](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)文章執行。

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

> [!NOTE]
> 對於**第三方廠商**所提供的映像，您必須指定另一個名為 `plan` 的屬性。 從快速入門資源庫的 [此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) 中可找到範例。 
> 
> 

您已定義好範本的資源。

## <a name="parameters"></a>parameters
在 parameters 區段中，定義可在部署範本時指定的值。 只針對您認為應在部署期間變動的值，定義參數。 如果部署期間並未提供預設值，您可以為所用的參數提供預設值。 如 **imageSKU** 參數所示，您也可以定義允許的值。

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>變數
在 variables 區段中，您可以定義在範本中多處使用的值，或從其他運算式或變數建構的值。 變數經常用來簡化範本的語法。

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

您已完成此範本！ 您可以比較您的範本與[具有負載平衡器和負載平衡器規則範本的 2 部 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) 之下[快速入門資源庫](https://github.com/Azure/azure-quickstart-templates)中的完整範本。 根據使用的版本號碼不同，您的範本可能會稍有不同。 

您可以使用在部署儲存體帳戶時所用的相同命令，重新部署範本。 在重新部署前，您不需要刪除儲存體帳戶，因為 Resource Manager 會略過重建已經存在且未變更的資源。

## <a name="next-steps"></a>後續步驟
* [Azure Resource Manager 範本視覺化檢視](http://armviz.io/#/) 是很棒的工具，可以視覺化方式檢視 Resource Manager 範本，因為範本可能會變得太大，以致無法僅從讀取 json 檔案了解。
* 若要深入了解範本的結構，請參閱 [編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)
* 如需關於自動化部署的四部分系列，請參閱[自動化應用程式部署至 Azure 虛擬機器](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 這個系列涵蓋應用程式架構、存取與安全性、可用性與級別，以及應用程式部署。




<!--HONumber=Dec16_HO1-->


