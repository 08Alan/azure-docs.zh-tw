

<properties
   pageTitle="Azure 應用程式閘道的健全狀況監視概觀 |Microsoft Azure"
   description="了解 Azure 應用程式閘道的監視功能"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="gwallace" />

# 應用程式閘道健全狀況監視概觀

Azure 應用程式閘道預設會監視其後端集區中所有資源的健康狀況，並自動從集區中移除任何被視為狀況不良的資源。應用程式閘道會繼續監視狀況不良的執行個體，一旦其恢復可用狀態並回應健康狀況探查，就會將其新增回狀況良好後端集區中。

![應用程式閘道探查範例][1]

除了使用預設的健全狀況探查監視，您也可以自訂健全狀況探查，以符合應用程式的需求。本文會探討預設和自訂健全狀態探查。

## 預設的健全狀況探查

如果您沒有設定任何自訂探查組態，應用程式閘道就會自動設定預設健全狀況探查。監視行為的運作方式是對後端集區所設定的 IP 位址提出 HTTP 要求。

例如：您將應用程式閘道設定為使用 A、B 和 C 後端伺服器來接收連接埠 80 上的 HTTP 網路流量。預設健全狀況監視每 30 秒就會對三部伺服器進行一次測試，以取得狀況良好的 HTTP 回應。狀況良好的 HTTP 回應具有 200 到 399 之間的[狀態碼](https://msdn.microsoft.com/library/aa287675.aspx)。

如果伺服器 A 的預設探查檢查失敗，應用程式閘道就會將其從後端集區中移除，網路流量也不會再流向此伺服器。預設探查仍會繼續每 30 秒檢查一次伺服器 A。當伺服器 A 成功回應預設健全狀態探查所提出的要求時，就會變為狀況良好並重新回到後端集區中，而流量也會開始再次流向該伺服器。

### 預設的健全狀況探查設定

|探查屬性 | 值 | 說明|
|---|---|---|
| 探查 URL| http://127.0.0.1:\<port>/ | URL 路徑 |
| 間隔 | 30 | 探查間隔 (秒) |
| 逾時 | 30 | 探查逾時 (秒) |
| 狀況不良臨界值 | 3 | 探查重試計數。連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

預設探查只會查看 http://127.0.0.1:\<連接埠> 來判斷健康狀態。如果您需要設定健全狀態探查，使其移至自訂 URL 或修改任何其他設定，則必須使用如下列步驟所述的自訂探查。

## 自訂的健全狀況探查

自訂探查可讓您更細微地控制健全狀況監視。使用自訂探查時，您可以設定探查間隔、要測試的 URL 和路徑，以及在將後端集區執行個體標示為狀況不良前，可接受的失敗回應次數。

### 自訂的健全狀況探查設定

|探查屬性| 說明|
|---|---|
| Name | 探查的名稱。此名稱用來在後端 HTTP 設定中指出探查。 |
| 通訊協定 | 用來傳送探查的通訊協定。HTTP 或 HTTPS 是有效的通訊協定。 |
| Host | 用來傳送探查的主機名稱。 |
| Path | 探查的相對路徑。有效路徑的開頭為 '/'。探查會傳送到 <protocol>://<host>:<port><path> |
| 間隔 | 探查間隔 (秒)。這是兩個連續探查之間的時間間隔。|
| 逾時 | 探查逾時 (秒)。如果在這個逾時期間內未收到有效的回應，則會將探查標示為失敗。 |
| 狀況不良臨界值 | 探查重試計數。連續探查失敗計數到達狀況不良臨界值後，就會將後端伺服器標示為故障。 |

## 後續步驟

在了解應用程式閘道的健全狀態監視之後，您可以在 Azure 入口網站中[自訂健全狀態探查](application-gateway-create-probe-portal.md)，或使用 PowerShell 和 Azure Resource Manager 部署模型設定[自訂健全狀態探查](application-gateway-create-probe-ps.md)。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

<!---HONumber=AcomDC_0907_2016-->