<properties 
	pageTitle="App Service 環境 | Microsoft Azure" 
	description="何謂 Azure App Service 環境？ App Service 環境簡介。" 
	keywords="azure app service 環境, 虛擬網路, 安全網路"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="stefsch"/>

# App Service 環境文件

App Service 環境是 Azure App Service 的[高階][PremiumTier]服務方案選項，可提供完全隔離的專用環境，以便安全地以高延展性執行 Azure App Service 應用程式，包括 [Web Apps][WebApps]、[行動應用程式][MobileApps]和 [API 應用程式][APIApps]。

適合應用程式工作負載的 App Service 環境需要：

- 非常高的延展性
- 隔離和安全的網路存取

客戶可以在單一 Azure 區域，以及跨多個 Azure 區域中建立多個 App Service 環境。這使得 App Service 環境很適合用來水平調整無狀態應用程式層的規模，以支援高 RPS 工作負載。

App Service 環境已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。使用[網路安全性群組][NetworkSecurityGroups]，客戶對於輸入和輸出的應用程式網路流量都能有更細微的控制。應用程式也可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

應用程式經常需要存取公司資源，例如內部資料庫和 Web 服務。App Service 環境上執行的應用程式可以存取能透過[站對站][SiteToSite] VPN 和 [Azure ExpressRoute][ExpressRoute] 連線存取的資源。

* [何謂 App Service 環境？](../app-service-web/app-service-app-service-environment-intro.md)
* [建立 App Service 環境](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [在 App Service 環境中建立應用程式](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [設定 App Service 環境](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [在 App Service 環境中調整應用程式](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [網路安全性與架構](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## 作法

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## 影片
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0511_2016-->