<properties
   pageTitle="Resource Manager 與傳統部署 | Microsoft Azure"
   description="描述資源管理員部署模型與傳統 (或服務管理) 部署模型之間的差異。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態

本主題中，您將了解 Azure Resource Manager 和傳統部署模型、您的資源的狀態、以及您的資源相互部署的原因。Resource Manager 部署模型與傳統部署模型有重要的差異，且兩個模型之間並非完全相容。為了簡化資源的部署和管理，Microsoft 建議您針對新的資源使用資源管理員，同時，可能的話，透過資源管理員重新部署現有的資源。

對大多數資源，您可以沒有任何問題地轉換為 Resource Manager。不過，由於模型之間的架構差異，一些資源提供者會提供兩個版本的資源 (一個用於傳統和一個用於資源管理員)。區別兩種模型之間的資源提供者是：

- **計算** - 支援虛擬機器的執行個體以及選擇性的可用性設定集。
- **儲存體** - 支援儲存虛擬機器之 VHD 所需的儲存體帳戶，包括它們的作業系統和其他資料磁碟。
- **網路** - 支援必要的 NIC、虛擬機器 IP 位址及虛擬網路內的子網路，以及選擇性的負載平衡器、負載平衡器 IP 位址與網路安全性群組。

針對這些資源類型，您必須知道您使用哪一個版本，因為支援的作業將會不同。如果您準備好將資源從傳統部署移轉至 Resource Manager 部署，請參閱[支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 的平台](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)。

為了解您的資源是以哪一個模型部署，讓我們來探討這兩個模型。

## 資源管理員特性

透過資源管理員建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

  - [Azure 入口網站](https://portal.azure.com/)。

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        For Compute, Storage, and Networking resources, you have the option of using either Resource Manager or Classic deployment. Select **Resource Manager**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - 若使用 Azure PowerShell，請使用 Resource Manager 版本的命令。這些命令的格式是 *Verb-AzureRmNoun*，如下所示。

            Get-AzureRmResourceGroupDeployment

  - 適用於 REST 作業的 [Azure 資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)。
  - Azure CLI 命令會在 **arm** 模式中執行。

            azure config mode arm

- 資源類型的名稱中不包括 **(傳統)**。下圖顯示的類型為**儲存體帳戶**。

    ![Web 應用程式](./media/resource-manager-deployment-model/resource-manager-type.png)

下圖所示的應用程式示範如何將透過資源管理員所部署的資源包含在單一資源群組中。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

此外，位於資源提供者內的資源之間具有關聯性：

- 虛擬機器取決於定義於 SRP 的特定儲存體帳戶，以將其磁碟儲存於 Blob 儲存體中。
- 虛擬機器會參考定義於 NRP 的特定 NIC (必要)，以及定義於 CRP 的可用性設定組 (選擇性)。
- NIC 會參考虛擬機器指派的 IP 位址 (必要)、虛擬機器之虛擬網路的子網路 (必要)，以及網路安全性群組 (選擇性)。
- 虛擬網路內的子網路會參考網路安全性群組 (選擇性)。
- 負載平衡器執行個體會參考 IP 位址的後端集區，其中包含虛擬機器的 NIC (選擇性)，以及參考負載平衡器的公用或私人 IP 位址 (選擇性)。

## 傳統部署特性

您可能也知道傳統部署模型也就是服務管理模型。

在 Azure 服務管理中，用以裝載虛擬機器的計算、儲存體或網路資源是透過下列方式來提供：

- 用來做為裝載虛擬機器 (計算) 之容器所需的雲端服務。虛擬機器是利用網路介面卡 (NIC) 和 Azure 所指派的 IP 位址自動提供。此外，雲端服務包含外部負載平衡器執行個體、共用 IP 位址及預設端點，以允許 Windows 架構虛擬機器的遠端桌面與遠端 PowerShell 流量，以及 Linux 架構虛擬機器的安全殼層 (SSH) 流量。
- 儲存虛擬機器之 VHD 所需的儲存體帳戶，包括作業系統、暫存磁碟及其他資料磁碟 (儲存體)。
- 可用來做為額外容器的選擇性虛擬網路，您可以在其中建立子網路的結構，並指定虛擬機器所在的子網路 (網路)。

在傳統部署模型中建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

  - [傳統入口網站](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the Azure portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - 若使用 Azure PowerShell，請使用「服務管理」版本的命令。這些命令名稱的格式是 *Verb-AzureNoun*，如下所示。

            Get-AzureDeployment

  - REST 作業的[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)。
  - Azure CLI 命令會在 **asm** 或預設模式中執行。
- 資源類型的名稱中包括 **(傳統)**。下圖顯示的類型為**儲存體帳戶 (傳統)**。

    ![傳統類型](./media/resource-manager-deployment-model/classic-type.png)

您仍然可以使用 Azure 入口網站，來管理透過傳統部署所建立的資源。

以下是 Azure 服務管理的元件及其關聯性。

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## 使用資源管理員及資源群組的優點

資源管理員加入資源群組的概念。透過資源管理員建立的每個資源都會存在於資源群組內。「資源管理員」部署模型提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有服務，而不是個別處理這些服務。
- 您可以在整個應用程式週期重複部署應用程式，並確信您的資源會部署在一致的狀態中。
- 您可以使用宣告式範本來定義您的部署。
- 您可以定義之間的相依性，使得以正確的順序部署資源。
- 由於角色型存取控制 (RBAC) 會原生整合至管理平台，因此您可以將存取控制套用至資源群組中的所有資源。
- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。


在資源管理員之前，您透過傳統部署建立的每個資源未存在於資源群組內。加入資源管理員時，所有資源會追溯地加入至預設資源群組。如果您現在透過傳統部署建立資源，資源會自動在該服務的預設資源群組內建立，即使您在部署時未指定該資源群組。不過，只是存在於資源群組內並不表示該資源已轉換成資源管理員模型。對於虛擬機器、儲存體和虛擬網路，如果是透過傳統部署建立資源，您必須繼續透過傳統作業來運作。

您可以將資源移至不同的資源群組，並將新的資源加入至現有的資源群組。因此，您的資源群組可以包含透過資源管理員和傳統部署建立之資源的混合。此資源的組合可能建立非預期的結果，因為資源不支援相同的作業。

透過使用宣告式範本，您可以簡化用於部署的指令碼。不要嘗試將現有指令碼從服務管理轉換為資源管理員，而是考慮重新執行您的部署策略以善用在範本中定義您的基礎結構和組態。

## 使用標記

標籤可讓您以邏輯方式組織您的資源。只有透過資源管理員建立的資源支援標記。您無法將標籤套用到傳統資源。

如需有關在資源管理員中使用標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。

## 部署模型支援的作業

在傳統部署模型中所建立的資源不支援資源管理員作業。在某些情況下，資源管理員命令可以擷取透過傳統部署建立之資源的相關資訊，或可以執行系統管理工作 (例如將傳統資源移至另一個資源群組)，但這些情況下，不應讓您認為該類型支援資源管理員作業。例如，假設您的資源群組包含使用傳統部署所建立的虛擬機器。若您執行下列 PowerShell 命令：

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

它會傳回虛擬機器：
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

不過，**Get-AzureRmVM** Cmdlet 只會傳回透過 Resource Manager 所部署的虛擬機器。下列命令不會傳回透過傳統部署所建立的虛擬機器。

    Get-AzureRmVM -ResourceGroupName ExampleGroup

一般而言，您不應該預期透過傳統部署所建立的資源可使用資源管理員命令。

使用透過資源管理員建立的資源時，您應該使用資源管理員作業，而不是切換回服務管理作業。

## 虛擬機器的考量

使用虛擬機器時有一些重要的考量。

- 使用傳統部署模型部署的虛擬機器不能包含在使用資源管理員部署的虛擬網路中。
- 使用資源管理員部署模型部署的虛擬機器必須包含在虛擬網路中。
- 使用傳統部署模型部署的虛擬機器不一定要包含在虛擬網路中。

如果您準備好將資源從傳統部署移轉至 Resource Manager 部署，請參閱[支援將 IaaS 資源從傳統移轉至 Azure Resource Manager 的平台](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)。

如需轉換運算、儲存體和網路資源的詳細資訊，請參閱 [Azure 資源管理員下的 Azure 運算、網路和儲存體提供者](./virtual-machines/virtual-machines-windows-compare-deployment-models.md)。

若要了解從不同部署模型連接虛擬網路，請參閱[將傳統 VNet 連接到新的 VNet](./virtual-network/virtual-networks-arm-asm-s2s.md)。

## 後續步驟

- 針對定義虛擬機器、儲存體帳戶和虛擬網路的範本，若需該範本的建立逐步解說，請參閱 [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)。
- 若要了解 Resource Manager 範本的結構，請參閱[製作 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
- 若要查看部署範本的命令，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0525_2016-->