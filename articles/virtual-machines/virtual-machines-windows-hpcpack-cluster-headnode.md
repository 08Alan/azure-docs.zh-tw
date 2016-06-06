<properties
 pageTitle="在 Azure VM 中建立 HPC Pack 前端節點 | Microsoft Azure"
 description="了解如何使用 Azure 入口網站和資源管理員部署模型，在 Azure VM 中建立 Microsoft HPC Pack 前端節點。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="05/19/2016"
 ms.author="danlep"/>

# 使用 Marketplace 映像在 Azure VM 中建立 HPC Pack 叢集的前端節點


使用 Azure Marketplace 中的 [Microsoft HPC Pack 虛擬機器映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)，以 Azure 入口網站建立 HPC 叢集的前端節點。此 HPC Pack VM 映像是基於已預先安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。使用此前端節點當作 Azure 中 HPC Pack 的概念證明部署。然後您可以將計算節點加入叢集以執行 HPC 工作負載。



>[AZURE.TIP]若要在包含前端節點和計算節點的 Azure 中進行完整的 HPC Pack 部署，我們建議您使用自動化的方法，例如 [HPC Pack IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)或 Resource Manager 範本，如 [HPC Pack cluster for Windows workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) (適用於 Windows 工作負載的 HPC Pack 叢集) 範本。如需其他範本，請參閱 [Azure 中的 HPC Pack 叢集選項](virtual-machines-windows-hpcpack-cluster-options.md)。


## 規劃考量

如下圖所示，您會將前端節點部署在 Azure 虛擬網路的 Active Directory 網域中。

![HPC Pack 前端節點][headnode]

* **Active Directory 網域** - 在您啟動 VM 上的 HPC 服務之前，HPC Pack 前端節點必須先加入 Azure 中的 Active Directory 網域。如本文中所示，針對概念證明部署，您可以在啟動 HPC 服務之前，將您為前端節點建立的 VM 升級為網域控制站。另一個選項則是將您要加入前端節點 VM 的網域控制站和樹系個別部署在 Azure 中。

* **Azure 虛擬網路** - 如本文中所示，當您使用 Azure 入口網站中的 Resource Manager 部署模型部署 HPC Pack 前端節點時，您會指定或建立 Azure 虛擬網路。您稍後需要使用虛擬網路來將叢集計算節點 VM 加入 HPC 叢集，或將前端節點加入現有的 Active Directory 網域。

    
## 建立前端節點的步驟

這些是使用 Azure 入口網站中的資源管理員部署模型為 HPC Pack 前端節點建立 Azure VM 的進階步驟。


1. 如果您想要以個別的網域控制站 VM 在 Azure 中建立新的 Active Directory 樹系，其中一個選項是使用 [Resource Manager 範本](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/)。如果只想要證明 HPC Pack 部署概念，可以省略它。而是要將前端節點 VM 本身設定成網域控制站，如本文稍後所述。
    
2. 若要在 [Azure 入口網站](https://portal.azure.com)中建立 HPC Pack 前端節點，請從 Azure Marketplace 選取 [HPC Pack 2012 R2] 映像。若要這樣做，請按一下 [新增] 並在 Marketplace 搜尋 **HPC Pack**。選取 [HPC Pack 2012 R2 on Windows Server 2012 R2] 映像。

3. 在 [HPC Pack 2012 R2 on Windows Server 2012 R2] 頁面上，選取 [Resource Manager] 部署模型，然後按一下 [建立]。

    ![HPC Pack 映像][marketplace]

4. 使用入口網站設定並建立該 VM。如不熟悉 Azure，請遵循教學課程[在 Azure 入口網站中建立 Windows 虛擬機器](virtual-machines-windows-hero-tutorial.md)進行。如要證明部署概念，您通常可以接受大部分的預設或建議設定。

    **虛擬網路考量事項**

   * 如果想要將前端節點加入 Azure 現有的 Active Directory 網域中，請務必在建立前端節點 VM 時，指定該網域的虛擬網路。
   
   * 如果想要建立新的虛擬網路，請在 [設定] 中指定私人網路位址範圍 (如 10.0.0.0/16)，以及子網路位址範圍 (如 10.0.0.0/24)。
    
4. 建立 VM 且 VM 開始執行之後，請透過遠端桌面[連接到 VM](virtual-machines-windows-connect-logon.md)。 

5. 將 VM 加入現有的網域樹系，或是在 VM 本身上建立新網域樹系。

    * 如果您在已具有網域樹系的 Azure 虛擬網路中建立 VM，請使用標準的伺服器管理員或 Windows PowerShell 工具將它加入網域樹系。然後重新啟動。

    * 如果您在無現有網域樹系的新虛擬網路中建立了 VM，則請將它升級為網域控制站。若要這樣做，請使用標準的伺服器管理員或 Windows PowerShell 工具來安裝並設定 Active Directory 網域服務角色。如需詳細步驟，請參閱[安裝新的 Windows Server 2012 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)。

5. VM 執行並加入 Active Directory 樹系之後，請在前端節點上啟動 HPC Pack 服務。作法：

    a.使用屬於本機 Administrators 群組成員的網域帳戶連接至 VM。例如，使用建立前端節點 VM 時所設定的系統管理員帳戶。

    b.針對預設前端節點組態，請以系統管理員身分啟動 Windows PowerShell，並輸入下列命令：

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    可能需要幾分鐘的時間，HPC Pack 服務才能啟動。

    如需其他前端節點組態選項，請輸入 `get-help HPCHNPrepare.ps1`。


## 後續步驟

* 您現在可以使用 HPC Pack 叢集的前端節點了。例如，啟動 HPC 叢集管理員，並完成[部署待辦事項清單](https://technet.microsoft.com/library/jj884141.aspx)。
* 在雲端服務中新增 [Azure 高載節點](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)，以視需要增加叢集計算容量。 

* 嘗試在叢集上執行測試工作負載。如需範例，請參閱 HPC Pack [快速入門指南](https://technet.microsoft.com/library/jj884144)。

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0525_2016-->