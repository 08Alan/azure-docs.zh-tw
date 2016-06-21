<properties
	pageTitle="Microsoft Azure 訂用帳戶及服務限制、配額與限制"
	description="提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。這包括如何增加限制和最大值的詳細資訊。"
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="jroth"/>

# Azure 訂用帳戶和服務限制、配額與限制

## 概觀

本文件說明一些最常見的 Microsoft Azure 限制。請注意，這目前未涵蓋所有 Azure 服務。這些限制將隨著時間擴展並更新以涵蓋更多平台。

> [AZURE.NOTE] 如果您想要將限制提升到**預設限制**以上，您可以[免費提出線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。您無法將限制提升到高於下表中所示的**上限**值。如果沒有**上限**欄，指定的資源即沒有可調整的限制。

## 限制和 Azure 資源管理員

現在您可以結合多個 Azure 中的資源到單一的 Azure 資源群組。使用資源群組時的限制是，在全域時會使用 Azure 資源管理員在地區層級管理。如需 Azure 資源群組的詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](azure-portal/resource-group-portal.md)。

在以下的限制中，已加入了新資料表，以反映在使用 Azure 資源管理員時的限制方面的任何差異。例如，有**訂用帳戶限制**資料表和**訂用帳戶限制 - Azure 資源管理員**資料表。當某個限制同時適用於這兩個案例時，只會顯示在第一個資料表中。除非另有說明，限制在所有區域中全域適用。

> [AZURE.NOTE] 請務必強調 Azure 資源群組中資源的配額是基於您的訂閱可以存取的每一區域，而不是每一訂閱 (服務管理配額則是)。讓我們以核心配額為例。如果您需要要求增加配額以支援核心，您必須決定您想要在哪些區域中使用多少個核心，然後提出 Azure 資源群組核心配額的特定要求，以取得您想要的數量和區域。因此，如果您需要在西歐使用 30 個核心以在該處執行應用程式，您應該在西歐特別要求 30 個核心。但是您在任何其他區域中的核心配額將不會增加 -- 僅西歐會有 30 個核心配額。
<!-- -->
因此，考慮決定每個區域中您的工作負載所需的 Azure 資源群組配額，並在要考慮部署的每個區域中要求該數量可能會有所幫助。請參閱[移難排解部署問題](./resource-manager-common-deployment-errors.md)，以取得探索您特定區域目前的配額的其他說明。


## 特定服務的限制

- [Active Directory](#active-directory-limits)
- [API 管理](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [自動化](#automation-limits)
- [Azure Redis 快取](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [備份](#backup-limits)
- [批次](#batch-limits)
- [BizTalk 服務](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [雲端服務](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [資料湖分析](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT 中心](#iot-hub-limits)
- [金鑰保存庫](#key-vault-limits)
- [媒體服務](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [行動服務](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [網路功能](#networking-limits)
- [通知中樞服務](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [資源群組](#resource-group-limits)
- [排程器](#scheduler-limits)
- [Search](#search-limits)
- [服務匯流排 (英文)](#service-bus-limits)
- [站台復原](#site-recovery-limits)
- [SQL Database](#sql-database-limits)
- [儲存體](#storage-limits)
- [StorSimple 系統](#storsimple-system-limits)
- [串流分析](#stream-analytics-limits)
- [訂用帳戶](#subscription-limits)
- [流量管理員](#traffic-manager-limits)
- [虛擬機器](#virtual-machines-limits)
- [虛擬機器擴展集](#virtual-machine-scale-sets-limits)


### 訂用帳戶限制
#### 訂用帳戶限制
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### 訂用帳戶限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### 資源群組限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### 虛擬機器限制
#### 虛擬機器限制
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### 虛擬機器限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。使用 Azure 資源管理員時未變更的限制不會在以下列出。請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### 虛擬機器擴展集限制

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### 網路限制

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### 網路限制
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### 流量管理員限制

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS 限制

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### 儲存體限制

如需儲存體帳戶限制的其他詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md)。

#### 儲存體服務限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 虛擬機器磁碟限制

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

如需其他詳細資訊，請參閱[虛擬機器大小](../articles/virtual-machines/virtual-machines-linux-sizes.md)。

**標準儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**進階儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### 儲存體資源提供者限制

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### 雲端服務限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### 應用程式服務限制
下列應用程式服務限制包含 Web 應用程式、行動應用程式、API 應用程式和邏輯應用程式的限制。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### 排程器限制

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch 限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk 服務限制
下表顯示 Azure Biztalk 服務的限制。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement 限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### 搜尋限制

定價層會決定搜尋服務的容量和限制。層級包括：

- 「免費」多租用戶服務，與其他 Azure 訂戶共用，適用於評估及小型開發專案。
- 「基本」可針對規模較小的生產工作負載提供專用的計算資源，以及針對高可用性的查詢工作負載提供最多 3 個複本。
- 「標準 (S1、S2、S3、S3 高密度)」適用於較大型生產工作負載。標準層內具有多個層級，如此就能讓您針對特定案例選擇資源組態。

**每一訂用帳戶的限制**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**每個搜尋服務的限制**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

如需其他限制的更細微資訊，包括文件大小、每秒的查詢數、金鑰、要求和回應，請參閱 [Azure 搜尋服務的服務限制](search/search-limits-quotas-capacity.md)。

### 媒體服務限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN 限制

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### 行動服務限制

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### 通知中樞服務限制

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### 服務匯流排限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT 中樞限制

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Data Factory 限制

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### 資料湖分析限制
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### 串流分析限制

| 限制識別碼 | 限制 | 註解 |
|----------------- | ------------|--------- |
| 每個區域的每個訂用帳戶串流單位數目上限 | 50 | 如需要求將訂用帳戶的串流單位數目增加至 50 個以上，請連絡 [Microsoft 支援](https://support.microsoft.com/zh-TW)。 |
| 串流單位的最大輸送量 | 1 MB/秒* | 每個 SU 的最大輸送量取決於視案例。實際的輸送量可能較低，而且取決於查詢複雜性和資料分割。可在[調整 Azure 串流分析工作以增加輸送量](../articles/stream-analytics/stream-analytics-scale-jobs.md)文章中找到進一步的詳細資料。 |

### Active Directory 限制

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple 系統限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights 限制

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### 備份限制

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Site Recovery 限制

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights 限制

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API 管理限制

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis 快取限制

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### 金鑰保存庫限制

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### 自動化限制
[AZURE.INCLUDE [自動化限制](../includes/azure-automation-service-limits.md)]

### SQL Database 限制

如需 SQL Database 的限制，請參閱 [SQL Database 資源限制](sql-database/sql-database-resource-limits.md)。

## 另請參閱

[了解 Azure 限制和增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure 的虛擬機器和雲端服務大小](virtual-machines/virtual-machines-linux-sizes.md)

[雲端服務的大小](cloud-services/cloud-services-sizes-specs.md)

<!---HONumber=AcomDC_0608_2016-->