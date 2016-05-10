<properties
 pageTitle="Azure IoT 中樞概觀 | Microsoft Azure"
 description="Azure IoT 中樞服務概觀：什麼是 IoT 中樞、裝置連線性、物聯網通訊模式和服務輔助通訊模式"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# 何謂 Azure IoT 中心？

歡迎使用 Azure IoT 中樞。本文提供 Azure IoT 中樞的概觀，並描述在實作物聯網 (IoT) 解決方案時，您應該使用此服務的原因。

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。Azure IoT 中樞：

- 提供可靠的裝置到雲端和雲端到裝置的大規模傳訊。
- 使用每一裝置的安全性認證和存取控制來啟用安全通訊。
- 可廣泛監視裝置的連線情況和裝置的身分識別管理事件。
- 包括適用於最受歡迎的語言和平台的裝置程式庫。

[IoT 中樞和事件中樞的比較][lnk-compare]一文描述這兩個服務之間的主要差異，並強調說明在 IoT 解決方案中使用 IoT 中樞的優點。

![Azure IoT 中樞做為物聯網解決方案中的雲端閘道][img-architecture]

> [AZURE.NOTE] 如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構][lnk-refarch]。

## IoT 裝置連線能力面臨的挑戰

對於以可靠且安全的方法將裝置連線到解決方案後端的挑戰，IoT 中樞和裝置程式庫可以協助解決。IoT 裝置：

- 通常是無人操作的嵌入式系統。
- 可以位於實體存取非常昂貴的遠端位置。
- 可能只能透過解決方案後端來存取。
- 能力和/或處理資源可能都有限。
- 網路連線能力可能不穩定、緩慢或昂貴。
- 可能需要使用專屬、自訂或業界特定的應用程式通訊協定。
- 可以使用一組大型常見的硬體和軟體平台來建立。

除了上述需求之外，任何 IoT 解決方案也必須提供調整、安全性和可靠性。當您使用傳統技術 (例如 Web 容器和傳訊代理程式) 時，實作連線需求的結果是困難且耗時的。

## 為何使用 Azure IoT 中心

Azure IoT 中樞會以下列方式解決裝置連線能力面臨的挑戰：

-   **每一裝置的驗證和安全連線能力**。您可以提供每個裝置獨有的[安全性金鑰][lnk-devguide-security]讓它連線到 IoT 中樞。[IoT 中樞身分識別註冊][lnk-devguide-identityregistry]會在解決方案中儲存裝置身分識別和金鑰。解決方案後端可將個別裝置加入白名單或黑名單，以達到完全控制裝置存取權。

-   **裝置連線作業的監視**。您可以收到有關裝置身分識別管理作業與裝置連線事件的詳細作業記錄檔。這可讓 IoT 解決方案輕鬆找出連線問題，例如，嘗試使用錯誤認證來連線的裝置、訊息傳送太頻繁，或拒絕所有雲端到裝置的訊息。

-   **一組廣泛的裝置程式庫**。[Azure IoT 裝置 SDK][lnk-device-sdks] 可供各種語言和平台使用並受其支援：C 表示許多 Linux 散發套件、Windows 和即時作業系統。Azure IoT 裝置 SDK 也支援 C#、Java 和 JavaScript 等 Managed 語言。

-   **IoT 通訊協定和擴充性**。如果您的解決方案不能使用裝置程式庫，Azure IoT 中樞會公開可讓裝置以原生方式使用 MQTT v3.1.1、HTTP 1.1 或 AMQP 1.0 通訊協定的公用通訊協定。您也可以擴充 IoT 中樞以提供自訂通訊協定支援，方法如下：

    - 使用 [Azure IoT 閘道 SDK][lnk-gateway-sdk] 建立領域閘道，以將自訂通訊協定轉換成 IoT 中樞所理解的三種通訊協定之一。 
    - 自訂 [Azure IoT 通訊協定閘道][protocol-gateway]，這是在雲端執行的開放原始碼元件。

-   **調整**。Azure IoT 中樞會調整為數百萬個同時連接的裝置，以及每秒數百萬個事件。

對於許多通訊模式這些優點都是通用的。IoT 中樞目前可讓您實作下列特定的通訊模式：

-   **以事件為基礎的裝置到雲端擷取。** IoT 中樞可以從您的裝置每秒可靠地收到數百萬個事件。它可以接著使用事件處理器引擎，在最忙碌路徑上處理這些事件。也可以將它們儲存在冷路徑上以供分析。IoT 中樞可保留最多 7 天的事件資料，以保證可靠的處理並吸收負載尖峰。

-   **可靠的雲端到裝置傳訊 (或「命令」)。** 解決方案後端可以使用 IoT 中樞傳送訊息給個別裝置 (含至少一次的傳遞保證)。每個訊息都有個別的存留時間設定，且後端可以要求傳遞和到期的回條。這可確保雲端到裝置訊息之生命週期的完整可見度。您就可以實作包含在裝置上執行之作業的商務邏輯。

利用 IoT 中樞裡的 IoT 特定功能，您也可以實作其他常見的模式，像是上傳和下載檔案。這些功能包括一致的裝置身分識別管理、連線能力監視和調整。

## 閘道

IoT 解決方案中的閘道器通常是部署於雲端中的[通訊協定閘道器][lnk-gateway]或使用您的裝置本機部署的[領域閘道器][lnk-field-gateway]。通訊協定閘道會執行通訊協定轉譯，例如 AMQP 到 MQTT。領域閘道可以在邊緣上執行分析、進行可降低延遲的時效性決策、提供裝置管理服務、強制執行安全性和隱私權條件約束，也可以執行通訊協定轉譯。這兩種閘道器可做為您的裝置與 IoT 中樞之間的媒介。

現場閘道器與簡單的流量路由裝置 (例如網路位址轉譯 (NAT) 裝置或防火牆) 不同，因為它通常會在解決方案內管理存取和資訊流程中扮演主動的角色。

解決方案可以同時包含通訊協定閘道和領域閘道。

## IoT 中樞如何運作？

Azure IoT 中樞會在實作[服務輔助通訊][lnk-service-assisted-pattern]模式時，調節您的裝置與解決方案後端之間的互動。服務輔助通訊的目標是要在控制系統 (例如 IoT 中樞) 以及特殊用途裝置 (部署在不可信任的實體空間中) 之間，建立可信任的雙向通訊路徑。該模式會建立下列原則：

- 安全性的優先順序高於所有其他功能。
- 裝置不會接受未經要求的網路資訊。裝置會以僅限輸出方式建立所有連接和路由。若要讓裝置從後端接收命令，裝置必須定期初始連接以檢查有沒有任何暫止的命令要處理。
- 裝置應該只連接至或是建立路由至與它們對等的已知服務 (例如 IoT 中樞)。
- 裝置和服務之間或裝置和閘道器之間的通訊路徑會在應用程式通訊協定層受到保護。
- 系統層級的授權和驗證是以每個裝置的身分識別為基礎。可讓存取認證和權限能近乎即時撤銷。
- 對於因為電源或連線能力而導致連線不穩定的裝置而言，可透過保留命令和通知直到裝置連線並接收它們，進而促進其雙向通訊。IoT 中樞會為傳送的命令維護裝置特定的佇列。
- 針對透過閘道對特定服務的受保護傳輸，應用程式承載資料會個別受到保護。

行動產業已大規模成功使用服務輔助通訊模式來實作推播通知服務，例如 [Windows 推播通知服務][lnk-wns]、[Google 雲端通訊][lnk-google-messaging]和 [Apple Push Notification Service][lnk-apple-push]。

## 後續步驟

若要深入了解 Azure IoT 中樞，請參閱這些連結：

* [開始使用 IoT 中心][lnk-get-started]
* [連接裝置][lnk-connect-device]
* [處理裝置到雲端的訊息][lnk-d2c]

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服務輔助通訊，由 Clemens Vasters 撰寫的部落格文章"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

<!---HONumber=AcomDC_0504_2016-->