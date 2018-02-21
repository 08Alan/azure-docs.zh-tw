---
title: "用於複寫至 Azure 的 Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2018
ms.author: rajanaki
ms.openlocfilehash: 426a456f8d979c8fb68b469f01eb68f378e876e8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>用於從內部部署複寫至 Azure 的 Azure Site Recovery 支援矩陣


本文摘要說明在複寫和復原到 Azure 時 Azure Site Recovery 支援的組態和元件。 如需 Azure Site Recovery 需求的相關資訊，請參閱[必要條件](site-recovery-prereq.md)。

> [!NOTE]
> 請務必將站台復原提供者和代理程式更新為最新版本，以透過支援矩陣中的更新實現相容性。


## <a name="support-for-deployment-options"></a>支援部署選項

**部署** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)** |
--- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至 Azure 儲存體，使用 Azure Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 型或傳統 VM。 | 內部部署 Hyper-V VM 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 型或傳統 VM。
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式。
**PowerShell** | 支援 | 支援


## <a name="support-for-datacenter-management-servers"></a>支援資料中心管理伺服器

### <a name="virtualization-management-entities"></a>虛擬化管理實體

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vCenter 6.5、6.0 或 5.5
**Hyper-V (有 Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 和 System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 System Center Virtual Machine Manager 2016 雲端。
  > 目前不支援包含將現有 SCVMM 2012 R2 升級至 2016 的設定。
### <a name="host-servers"></a>主機伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vSphere 6.5、6.0、5.5
**Hyper-V (含/不含 Virtual Machine Manager)** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2。<br></br>Windows Server 2016 主機應由 SCVMM 2016 所管理 (若使用 SCVMM)。


  >[!Note]
  >目前不支援混用執行 Windows Server 2016 和 2012 R2 之主機的 Hyper-V 網站。 目前不支援將 Windows Server 2016 主機上的 VM 復原到替代位置。

## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本

複寫至 Azure 時，受保護的虛擬機器必須符合 [Azure 需求](#failed-over-azure-vm-requirements)。
下表摘要說明使用 Azure Site Recovery 時各種部署案例中的複寫作業系統支援。 這項支援適用於在上述 OS 中執行的任何工作負載。

 **VMware/實體伺服器** | **Hyper-V (含/不含 VMM)** |
--- | --- |
64 位元 Windows Server 2016 (Server Core，使用桌面體驗的伺服器)\*、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少加裝 SP1)<br/><br/> Red Hat Enterprise Linux：5.2 至 5.11、6.1 至 6.9、7.0 至 7.4<br/><br/>CentOS：5.2 至 5.11、6.1 至 6.9、7.0 至 7.4 <br/><br/>Ubuntu 14.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(不支援 SLES 11 SP3 至 SLES 11 SP4 的複寫電腦升級。 若已將複寫電腦從 SLES 11SP3 升級至 SLES 11 SP4，則您必須停用複寫以在升級後重新提供電腦防護。） | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS

>[!NOTE]
>
> 不支援 \* Windows Server 2016 Nano Server。
>
> 在 Linux 散發套件上，僅支援屬於散發套件的次要版本/更新的庫存核心。
>
> 不支援在 Azure Site Recovery 受保護的 VMware 虛擬機器或實體伺服器上對 Linux 散發套件的主要版本進行升級。 升級主要版本 (例如 CentOS 6.* 至 CentOS 7.*) 的作業系統時，請停用對機器的複寫，並升級機器的作業系統，然後再次啟用複寫。
> 


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>VMware/實體伺服器支援的 Ubuntu 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic 至 3.13.0-121-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 至 3.13.0-128-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic 至 3.13.0-132-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic 至 3.13.0-137-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 至 4.4.0-81-generic、<br/>4.8.0-34-generic 至 4.8.0-56-generic、<br/>4.10.0-14-generic 至 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 至 4.4.0-91-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic 至 4.4.0-96-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic 至 4.4.0-104-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Linux 上的支援檔案系統與客體儲存體組態 (VMware/實體伺服器)

執行 VMware 或實體伺服器的 Linux 伺服器，支援下列檔案系統與儲存體組態：
* 檔案系統：ext3、ext4、ReiserFS (僅 Suse Linux Enterprise Server)、XFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式

不支援並行虛擬存放裝置 (由並行虛擬驅動程式匯出的裝置)。<br/>
不支援多佇列區塊 IO 裝置。<br/>
不支援使用 HP CCISS 儲存體控制器的實體伺服器。<br/>

>[!Note]
> 在 Linux 伺服器上，下列目錄必須一律位於來源伺服器的同個磁碟 (OS 磁碟) (若設為獨立資料分割/檔案系統)：/(root)、/boot、/usr、/usr/local、/var、/ 等等，而且 /boot 應該在磁碟分割上，而非在 LVM 磁碟區上<br/><br/>
>


## <a name="support-for-network-configuration"></a>支援網路組態
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 的各種部署案例中的網路組態支援。

### <a name="host-network-configuration"></a>主機網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
NIC Teaming | yes<br/><br/>複寫實體機器時不支援| yes
VLAN | yes | yes
IPv4 | yes | yes
IPv6 | 否 | 否

### <a name="guest-vm-network-configuration"></a>客體 VM 網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
NIC Teaming | 否 | 否
IPv4 | yes | yes
IPv6 | 否 | 否
靜態 IP (Windows) | yes | yes
靜態 IP (Linux) | yes <br/><br/>虛擬機器設定為在容錯回復時使用 DHCP  | 否
多個 NIC | yes | yes

### <a name="failed-over-azure-vm-network-configuration"></a>容錯移轉的 Azure VM 網路組態

**Azure 網路功能** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | yes | yes
ILB | yes | yes
ELB | yes | yes
流量管理員 | yes | yes
多個 NIC | yes | yes
保留的 IP | yes | yes
IPv4 | yes | yes
保留來源 IP | yes | yes
虛擬網路服務端點 (Azure 儲存體防火牆與虛擬網路) | 否 | 否


## <a name="support-for-storage"></a>儲存體的支援
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 的各種部署案例中的儲存體組態支援。

### <a name="host-storage-configuration"></a>主機儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | --- | ---
NFS | VMware 為是<br/><br/> 實體伺服器為否 | N/A
SMB 3.0 | N/A | yes
SAN (ISCSI) | yes | yes
多重路徑 (MPIO)<br></br>測試標的︰Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | yes | yes

### <a name="guest-or-physical-server-storage-configuration"></a>客體或實體伺服器儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
VMDK | yes | N/A
VHD/VHDX | N/A | yes
第 2 代 VM | N/A | yes
EFI/UEFI| 僅適用於 Windows Server 2012 和更新版本移轉到 Azure。 </br></br> ** 請參閱表格結尾處的附註。  | yes
共用叢集磁碟 | 否 | 否
已加密磁碟 | 否 | 否
NFS | 否 | N/A
SMB 3.0 | 否 | 否
RDM | yes<br/><br/> 實體伺服器為 N/A | N/A
磁碟 > 1 TB | yes<br/><br/>最大 4095 GB | yes<br/><br/>最大 4095 GB
4k 邏輯與 4k 實體磁區大小的磁碟 | yes | 不支援第 1 代 VM<br/><br/>不支援第 2 代 VM。
4k 邏輯與 512 位元組實體磁區大小的磁碟 | yes |  yes
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM 邏輯磁碟區管理 | yes | yes
儲存空間 | 否 | yes
熱新增/移除磁碟 | 否 | 否
排除磁碟 | yes | yes
多重路徑 (MPIO) | N/A | yes

> [!NOTE]
> ** UEFI 開機 VMware 虛擬機器，或執行 Windows Server 2012 或更新版本的實體伺服器，都可以移轉到 Azure。 適用以下限制。
> - 僅移轉到 Azure。 不支援容錯回復至內部部署 VMware 網站。
> - 不支援在伺服器的 OS 磁碟上有超過 4 個磁碟分割。
> - 需要 Azure Site Recovery 行動服務 9.13 版或更新版本。

**Azure 儲存體** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
LRS | yes | yes
GRS | yes | yes
RA-GRS | yes | yes
非經常性儲存體 | 否 | 否
經常性存取儲存體| 否 | 否
區塊 Blob | 否 | 否
待用加密 (SSE)| yes | yes
進階儲存體 | yes | yes
匯入/匯出服務 | 否 | 否
用來儲存複寫資料之目標儲存體帳戶或快取儲存體帳戶上設定的虛擬網路服務端點 (Azure 儲存體防火牆與虛擬網路) | 否 | 否
一般用途 V2 儲存體帳戶 (經常性存取層和非經常性存取層) | 否 | 否


## <a name="support-for-azure-compute-configuration"></a>支援 Azure 計算設定

**計算功能** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
可用性設定組 | yes | yes
中樞 | yes | yes  
受控磁碟 | yes | yes<br/><br/>目前不支援從具有受控磁碟的 Azure VM 容錯回復至內部部署。

## <a name="failed-over-azure-vm-requirements"></a>容錯移轉的 Azure VM 需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 複寫至 Azure 時，您想要複寫的內部部署 VM 必須符合下列 Azure 需求。

**實體** | **需求** | **詳細資料**
--- | --- | ---
**客體作業系統** | 從 Hyper-V 複寫到 Azure：Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。 <br/><br/> 針對 VMware 和實體伺服器複寫：請檢查 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure-classic.md) | 如果不支援，則先決條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟大小** | 如果您要將 **VMware VM 或實體伺服器複寫至 Azure**，則最多 2048 GB。<br/><br/>對於 **Hyper-V 第 1 代** VM，最多 2048 GB。<br/><br/>對於 **Hyper-V 第 2 代 VM**，最多 300 GB。  | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟計數** | 1 | 如果不支援，則先決條件檢查會失敗。
**資料磁碟計數** | 如果您要複寫 **VMware VM 至 Azure**，則為 64 或低於 64；如果您要複寫 **Hyper-V VM 至 Azure**，則為 16 或低於 16 | 如果不支援，則先決條件檢查會失敗
**資料磁碟 VHD 大小** | 最多 4095 GB | 如果不支援，則先決條件檢查會失敗
**網路介面卡** | 支援多個介面卡 |
**共用 VHD** | 不支援 | 如果不支援，則先決條件檢查會失敗
**FC 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗
**硬碟格式** | VHD  <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援 | 必須停用 Bitlocker，才能保護虛擬機器。
**VM 名稱** | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。 | 更新 Site Recovery 中虛擬機器屬性的值。
**VM type** | 第 1 代<br/><br/> 第 2 代 -- Windows | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>支援復原服務保存庫動作

**Action** | **VMware/實體伺服器** | **Hyper-V (無 Virtual Machine Manager)** | **Hyper-V (有 Virtual Machine Manager)**
--- | --- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否


## <a name="support-for-provider-and-agent"></a>支援提供者和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 如果沒有 Virtual Machine Manager 伺服器，安裝在內部部署 Virtual Machine Manager 伺服器或 Hyper-V 伺服器上 | 5.1.2700.1 (可從入口網站取得) | [最新功能和修正](https://aka.ms/latest_asr_updates)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9.12.4653.1 (可從入口網站取得) | [最新功能和修正](https://aka.ms/latest_asr_updates)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | 9.12.4653.1 (可從入口網站取得) | [最新功能和修正](https://aka.ms/latest_asr_updates)
**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 Hyper-V 伺服器上 (無論是否有 Virtual Machine Manager 伺服器) | 最新的代理程式 (可從入口網站取得) |






## <a name="next-steps"></a>後續步驟
[檢查必要條件](site-recovery-prereq.md)
