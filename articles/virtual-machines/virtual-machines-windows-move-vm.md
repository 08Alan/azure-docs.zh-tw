<properties
	pageTitle="移除 Windows VM | Microsoft Azure"
	description="在 Resource Manager 部署模型中將 Windows VM 移至另一個 Azure 訂用帳戶或資源群組。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>

	


# 將 Windows VM 移至另一個 Azure 訂用帳戶或資源群組 

本文將逐步引導您了解如何在資源群組或訂用帳戶之間移動 Windows VM。如果您原本在個人訂用帳戶中建立 VM，而現在想要將它移至您的公司訂用帳戶以繼續工作，在訂用帳戶之間移動會很方便。

> [AZURE.NOTE] 移動過程中會建立新的資源識別碼。移動 VM 之後，您必須更新工具和指令碼以使用新的資源識別碼。


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## 使用 PowerShell 移動 VM

若要將虛擬機器移至另一個資源群組，您必須確定也要移動所有的相依資源。若要使用 Move-AzureRMResource Cmdlet，您需要資源名稱和資源類型。您可以從 Find-AzureRMResource Cmdlet 取得這兩個項目。

	Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
	

若要移動 VM，我們需要移動多個資源。我們可以為每個資源建立不同的變數，然後加以列出。此範例包含 VM 大部分的基本資源，但您可以視需要加入更多資源。

	$sourceRG = "<sourceResourceGroupName>"
	$destinationRG = "<destinationResourceGroupName>"
    
	$vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
	$diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
	$vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
	$nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
	$ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
	$nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
	
	Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

若要將資源移到不同的訂用帳戶，請納入 **DestinationSubscriptionId** 參數。

	Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



系統會要求您確認您想要移動指定的資源。輸入 **Y** 確認您要移除資源。

  
## 後續步驟

您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。如需詳細資訊，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。

<!---HONumber=AcomDC_0810_2016---->