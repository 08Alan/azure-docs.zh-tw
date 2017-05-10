---
title: "在 Azure AD 應用程式 Proxy 中使用連接器群組在個別的網路和位置上發佈應用程式 | Microsoft Docs"
description: "涵蓋如何建立和管理「Azure AD 應用程式 Proxy」中的連接器群組。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 39049c7a1e2a4d61ef62bd06cda9ef1bb2c50c0b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/05/2017

---

# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>使用連接器群組在個別的網路和位置上發佈應用程式
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-application-proxy-connectors-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-application-proxy-connectors.md)
>

## <a name="azure-ad-application-proxy-and-connector-groups"></a>Azure AD 應用程式 Proxy 與連接器群組

客戶在越來越多的案例和應用程式中利用 Azure AD 的應用程式 Proxy。 因此我們啟用更多的拓撲使應用程式 Proxy 更具彈性。 您可以建立應用程式 Proxy 連接器群組 – 可指派特定連接器以提供特定應用程式的新功能。 這項功能為應用程式 Proxy 產生過去不可能的許多使用案例。 

基本概念是，每個應用程式 Proxy 連接器都會指派給連接器群組。 隸屬於相同連接器群組的所有連接器會做為高可用性及負載平衡的個別群組。 根據預設，所有的連接器屬於預設群組。 系統管理員可以建立新的群組，並在 Azure 入口網站變更這些指派。 

根據預設，所有應用程式會指派給預設連接器群組。 如果您的系統管理員不會變更任何項目，系統會繼續像以前一樣的行為。 如果您沒有變更任何項目，指派給預設連接器群組的所有應用程式會包含所有連接器。 但是，如果您將連接器組織成群組，可以設定每個應用程式使用特定連接器群組。 在此情況下，只有該群組中的連接器會在收到要求時為應用程式提供服務。


>[!NOTE] 
>因為新的連接器會自動指派給預設連接器群組，針對大型部署，我們建議您不要將應用程式指派給預設群組。 因此，一旦安裝完成後，新的連接器不會收到任何即時流量。 只有在將連接器指派給其中一個使用中的群組之後，才可以開始提供即時流量。 這也可讓您將連接器處於閒置模式以啟用維護。
>

## <a name="prerequisite-create-your-connector-groups"></a>必要條件︰建立連接器群組
若要將連接器組成群組，您必須確定 [已安裝多個連接器](active-directory-application-proxy-enable.md)。 當您安裝新的連接器時，它會自動加入「預設」  連接器群組。

## <a name="step-1-create-connector-groups"></a>步驟 1：建立連接器群組
您可以建立任意數量的連接器群組。 連接器群組的建立是在 [Azure 入口網站](https://portal.azure.com)中完成。

1. 選取 **Azure Active Directory** 以移至您目錄的管理儀表板。 從該處選取 [企業應用程式] > [應用程式 Proxy]。
2. 選取 [連接器群組]  按鈕。 [新增連接器群組] 刀鋒視窗隨即出現。
3. 指定新連接器群組的名稱，然後使用下拉式功能表來選取哪些連接器屬於此群組。
4. 完成連接器群組時，選取 [儲存]  。

## <a name="step-2-assign-applications-to-your-connector-groups"></a>步驟 2：將應用程式指派給您的連接器群組
最後一個步驟是將每個應用程式設定至將為其提供服務的連接器群組。

1. 從您目錄的管理儀表板中，選取 [企業應用程式] > [所有應用程式] > 您想要指派給連接器群組的應用程式 > [應用程式 Proxy]。
2. 在 [連接器群組] 底下，使用下拉式功能表來選取應用程式所要使用的群組。
3. 按一下 [儲存]  以套用變更。

## <a name="use-cases-for-connector-groups"></a>連接器群組的使用案例 

連接器群組可用於多種不同狀況，包括：

###<a name="sites-with-multiple-interconnected-datacenters"></a>具有多個互連資料中心的網站

許多組織都有數個彼此連接的資料中心。 在此情況下，您會希望儘量將流量保留在資料中心內，因為跨資料中心的連結既昂貴、速度又慢。 您可以在每個資料中心都部署連接器，來只為資料中心內的應用程式提供服務。 這種方法可將跨資料中心的連結減到最少，讓使用者體驗完全的流暢性。

### <a name="applications-installed-on-isolated-networks"></a>安裝在隔離網路上的應用程式

應用程式可以在不是主要的公司網路一部分的網路上託管。 您可以使用連接器群組將專用連接器安裝在隔離網路上，以同時將應用程式與網路隔離。 當協力廠商為您的組織維護特定應用程式時，就會發生這種情形。 

連接器群組可讓您安裝僅發佈特定應用程式之網路的專用連接器，使外包應用程式管理給協力廠商更輕鬆且更安全。

### <a name="applications-installed-on-iaas"></a>IaaS 上已安裝應用程式 

對於安裝在 IaaS 上以供雲端存取的應用程式，連接器群組提供通用的服務來保護對所有應用程式的存取。 連接器群組不會對公司網路產生額外的相依性，或是造成不完整的應用程式體驗。 連接器可安裝在每個雲端資料中心，而且只為此網路中的應用程式提供服務。 您可以安裝數個連接器以獲得高可用性。

在此情況下，組織會有數個連線到其 IaaS 主控之虛擬網路的虛擬機器。 若要允許員工使用這些應用程式，這些私人網路會使用站台對站 VPN 連線到公司網路。 針對位於內部部署的員工，這會提供良好的體驗。 但是，它可能不適合遠端員工，因為它需要額外內部部署基礎結構，如您所見下圖顯示︰

![AzureAD Iaas 網路](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
當許多組織使用多個雲端供應商時，這可能會產生問題，因為他們的應用程式位於多個資料中心。 使用 Azure AD 應用程式 Proxy 連接器群組，您可以啟用一般服務來保護所有應用程式，而無須在您的公司網路上建立其他相依性︰

![AzureAD Iaas 多雲端廠商](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>多樹系 – 每個樹系不同的連接器群組

大部分已部署應用程式 Proxy 的客戶會執行 Kerberos 限制委派 (KCD) 來使用單一登入 (SSO) 功能。 若要達到此目的，連接器的電腦需要加入可將使用者委派至應用程式的網域。 KCD 支援跨樹系功能。 但對於擁有不同的多樹系環境且它們之間沒有信任的公司來說，單一連接器無法用於所有的樹系。 

在此情況下，每個樹系可以部署特定連接器，並設定為僅為該特定樹系使用者提供服務所發佈的應用程式提供服務。 每個連接器群組代表不同的樹系。 雖然租用戶和大部分的體驗會針對所有樹系進行整合，使用者可以使用 Azure AD 群組指派給其樹系的應用程式。
 
### <a name="disaster-recovery-sites"></a>災害復原網站

您可以根據您站台的實作方式，使用災害復原 (DR) 網站採用兩種不同的方法︰

* 如果您的 DR 網站是在主動-主動模式中建置，其中與主要站台完全相同，且具有相同的網路與 AD 設定，您可以在與主要站台相同的連接器群組中，在 DR 網站上建立連接器。 這可讓 Azure AD 為您偵測容錯移轉。
* 如果 DR 網站是獨立於主要網站，您可以在 DR 網站中建立不同的連接器群組，並有 1) 其他應用程式或 2) 視需要手動將現有的應用程式轉向至 DR 連接器群組。
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>從單一租用戶為多家公司提供服務

有許多不同的方式可實作模型，其中單一服務提供者可部署及為多家公司維護 Azure AD 的相關服務。 連接器群組可協助系統管理員將連接器和應用程式分成不同的群組。 適合小型公司的一個方法是擁有單一 Azure AD 租用戶，同時不同公司都有自己的網域名稱和網路。 M&A 案例和情況也是如此，其中單一 IT 部門會針對規範或企業原因為幾家公司提供服務。 

## <a name="sample-configurations"></a>範例組態

您可以實作一些範例，包括下列連接器群組。
 
### <a name="default-configuration--no-use-for-connector-groups"></a>預設組態 – 不使用連接器群組

如果您不使用連接器群組，您的組態會看起來像這樣︰

![AzureAD 沒有連接器群組](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
這個組態就對小型部署和測試就已足夠。 如果您的組織具有平面網路拓撲，則它也會正常運作。
 
### <a name="default-configuration-and-an-isolated-network"></a>預設組態和隔離的網路

此組態是預設的演化，其中有在隔離網路 (例如 IaaS 虛擬網路) 中執行的特定應用程式︰ 

![AzureAD 沒有連接器群組](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>建議的組態 – 幾個特定的群組和預設的閒置群組

大型及複雜組織的建議組態是具有預設連接器群組做為不提供任何應用程式服務並用於閒置或新安裝連接器的群組。 所有應用程式會使用自訂的連接器群組來提供服務。 這可啟用上述案例的所有複雜度。

下列範例中，公司有兩個資料中心 (A 和 B)，並具有兩個連接器可為每個站台提供服務。 每個網站都有不同的應用程式在其上執行。 

![AzureAD 沒有連接器群組](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>後續步驟
* [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)


