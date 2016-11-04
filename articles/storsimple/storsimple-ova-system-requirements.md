---
title: StorSimple Virtual Array 系統需求
description: 了解 StorSimple Virtual Array 的軟體和網路功能需求
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/14/2016
ms.author: alkohli

---
# StorSimple Virtual Array 系統需求
## 概觀
本文說明 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置) 和存取陣列的儲存體用戶端重要的系統需求。建議您先仔細檢閱資訊，再部署 StorSimple 系統，然後在部署和後續作業期間，必要時回顧參考。

系統需求包括：

* **儲存體用戶端的軟體需求** - 描述支援的虛擬化平台、網頁瀏覽器、iSCSI 啟動器、SMB 用戶端、基本虛擬裝置需求和那些作業系統的任何額外需求。
* **StorSimple 裝置的網路需求** - 提供需要在防火牆中開啟以允許 iSCSI、雲端或管理流量的連接埠相關資訊。

這篇文章中的 StorSimple 系統需求資訊，僅適用於 StorSimple Virtual Array。

* 針對 8000 系列裝置，請移至 [StorSimple 8000 系列裝置的系統需求](storsimple-system-requirements.md)。
* 針對 7000 系列裝置，請移至 [System requirements for your StorSimple 5000-7000 series device (StorSimple 5000-7000 系列裝置的系統需求)](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)。

## 軟體需求
軟體需求包括支援的網頁瀏覽器、SMB 版本、虛擬化平台和最低虛擬裝置需求的資訊。

### 支援的虛擬化平台
| **Hypervisor** | **版本** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 和更新版本 |
| VMware ESXi |5\.5 和更新版本 |

### 虛擬裝置需求
| **元件** | **需求** |
| --- | --- |
| 虛擬處理器 (核心) 的最小數目 |4 |
| 最小記憶體 (RAM) |8 GB |
| 磁碟空間<sup>1</sup> |OS 磁碟 - 80 GB <br></br>資料磁碟 - 500 GB 到 8 TB |
| 最小網路介面數目 |1 |
| 最小網際網路頻寬<sup>2</sup> |5 Mbps |

<sup>1</sup> - 精簡佈建

<sup>2</sup> - 網路需求可能會依據每日資料變更率而有所不同。例如，如果裝置一天需要備份 10 GB 或更多的變更，則透過 5 Mbps 連線的每日備份最多可能需要 4.25 小時 (如果資料無法壓縮或刪除重複資料)。

### 支援的網頁瀏覽器
| **元件** | **版本** | **其他需求/注意事項** |
| --- | --- | --- |
| Microsoft Edge |最新版本 | |
| Internet Explorer |最新版本 |通過 Internet Explorer 11 測試 |
| Google Chrome |最新版本 |通過 Chrome 46 測試 |

### 支援的儲存體用戶端
下列軟體需求適用於存取 StorSimple Virtual Array (已設定為 iSCSI 伺服器) 的 iSCSI 啟動器。

| **受支援的作業系統** | **必要版本** | **其他需求/注意事項** |
| --- | --- | --- |
| Windows Server |2008R2 SP1、2012、2012R2 |StorSimple 可以建立精簡佈建和完整佈建的磁碟區。它無法建立部分佈建的磁碟區。只有針對下列對象才支援 StorSimple iSCSI 磁碟區︰<ul><li>Windows 基本磁碟上的簡單磁碟區。</li><li>用於將磁碟區格式化的 Windows NTFS。</li> |

下列軟體需求適用於存取 StorSimple Virtual Array (已設定為檔案伺服器) 的 SMB 用戶端。

| **SMB 版本** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## 網路需求
下表列出必須在您的防火牆中開啟的連接埠，以允許 iSCSI、SMB、雲端或管理流量。在這個資料表中，*in* 或 *inbound* 指的是輸入用戶端要求存取裝置的方向。*Out* 或 *outbound* 指的是 StorSimple 裝置於外部傳送資料至部署之上的方向：例如，輸出到網際網路。

| **連接埠號碼<sup>1</sup>** | **內或外** | **連接埠範圍** | **必要** | **注意事項** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |外 |WAN |否 |輸出連接埠用於網際網路存取以擷取更新。<br></br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS) |外 |WAN |是 |輸出連接埠用來存取雲端中的資料。<br></br>輸出 Web Proxy 可由使用者設定。 |
| UDP 53 (DNS) |外 |WAN |在某些情況下，請參閱附註。 |只有當您使用網際網路 DNS 伺服器時，才需要此連接埠。<br></br> **注意**：如果部署的是檔案伺服器，建議使用本機 DNS 伺服器。 |
| UDP 123 (NTP) |外 |WAN |在某些情況下，請參閱附註。 |只有當您使用以網際網路為基礎的 NTP 伺服器時，才需要此連接埠。<br></br> **注意：**如果部署的是檔案伺服器，建議將時間與您的 Active Directory 網域控制站同步。 |
| TCP 80 (HTTP) |在 |LAN |是 |這是 StorSimple 裝置上用於本機管理的本機 UI 的輸入連接埠。<br></br> **注意**：透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。 |
| TCP 443 (HTTPS) |在 |LAN |是 |這是 StorSimple 裝置上用於本機管理的本機 UI 的輸入連接埠。 |
| TCP 3260 (iSCSI) |在 |LAN |否 |此連接埠用來透過 iSCSI 存取資料。 |

<sup>1</sup> 公用網際網路上沒有必須開啟的輸入連接埠。

> [!IMPORTANT]
> 請確定防火牆不會修改或解密 StorSimple 裝置和 Azure 之間的任何 SSL 流量。
> 
> 

### 防火牆規則的 URL 模式
網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。您的虛擬陣列和 StorSimple Manager 服務取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶和 Microsoft Update 伺服器。與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。請務必了解與這些應用程式相關聯的 URL 模式可以變更。接著，您將需要網路系統管理員監控 StorSimple 的防火牆規則，並在需要時更新。

我們建議您，在大部分情況下，根據固定 IP 位址為輸出流量設定防火牆規則。不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> * 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> * 目的地 IP 應該設定為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/zh-TW/download/confirmation.aspx?id=41653)。
> 
> 

| URL 模式 | 元件/功能 |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` |StorSimple Manager 服務<br>存取控制服務<br>Azure 服務匯流排 |
| `http://*.backup.windowsazure.com` |裝置註冊 |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |憑證撤銷 |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure 儲存體帳戶和監視 |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Microsoft Update 伺服器<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |支援封裝 |
| `http://*.data.microsoft.com ` |Windows 中的遙測服務，請參閱[客戶經驗和診斷遙測的更新](https://support.microsoft.com/zh-TW/kb/3068708) |

## 後續步驟
* [Prepare the portal to deploy your StorSimple Virtual Array (準備入口網站以部署 StorSimple Virtual Array)](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0720_2016-->