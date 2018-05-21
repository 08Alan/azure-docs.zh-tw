---
title: 了解 Azure IoT 中樞直接方法 | Microsoft Docs
description: 開發人員指南 - 從服務應用程式使用直接方法叫用您裝置上的程式碼。
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: ''
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a35c88ac053b43d4a95b5bef92f3ebfb03567e2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>了解 IoT 中樞的直接方法並從中樞叫用直接方法
「IoT 中樞」能讓您從雲端在裝置上叫用直接方法。 直接方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)。 針對立即動作的進展取決於裝置是否能夠回應的案例，此方法會相當有用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

每個裝置方法的目標是單一裝置。 [作業][lnk-devguide-jobs]提供方法來在多個裝置上叫用直接方法，並針對已中斷連接的裝置排定方法引動過程。

IoT 中樞上具有**服務連線**權限的任何人都可以叫用裝置上的方法。

直接方法會依循「要求-回應」模式，適用於需要立即確認其結果的通訊。 例如，裝置的互動式控制，像是開啟風扇。

如果不確定要使用所需屬性、直接方法或雲端對裝置訊息，請參閱[雲端對裝置通訊指引][lnk-c2d-guidance]。

## <a name="method-lifecycle"></a>方法生命週期
直接方法是在裝置上實作，而且可能需要方法承載中的零或多個輸入以正確具現化。 您可以透過面向服務的 URI 叫用直接方法 (`{iot hub}/twins/{device id}/methods/`)。 裝置會透過裝置特定的 MQTT 主題 (`$iothub/methods/POST/{method name}/`) 或透過 AMQP 連結 (`IoThub-methodname` 和 `IoThub-status` 應用程式屬性) 收到直接方法。 

> [!NOTE]
> 當您在裝置上叫用直接方法時，屬性名稱和值只能包含 US-ASCII 可列印英數字元，下列集合中的任何字元除外︰``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``。
> 
> 

直接方法是同步的，可能在逾時期間後成功或失敗 (預設︰30 秒，可設定為 3600 秒)。 在您想要讓裝置只有在線上且接收命令的情況下才採取行動的互動式案例中，直接方法相當有用。 例如，透過手機開燈。 在這些案例中，您想要查看立即成功或失敗，讓雲端服務可以儘速處理結果。 裝置可能會傳回部分訊息本文作為方法的結果，但是不需要方法這麼做。 不保證方法呼叫的順序或任何並行語意。

直接方法從雲端來說，僅限使用 HTTPS，從裝置端來說，則使用 MQTT 或 AMQP。

方法要求和回應的承載是一個 JSON 文件 (大小上限為 128 KB)。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>從後端應用程式叫用直接方法
### <a name="method-invocation"></a>方法引動過程
裝置上的直接方法引動過程是 HTTPS 呼叫，由下列各項組成︰

* 裝置特定的 *URI* (`{iot hub}/twins/{device id}/methods/`)
* POST *方法*
* *標頭* - 包含授權、要求識別碼、內容類型及內容編碼
* 透明 JSON *本文*格式如下︰

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

逾時 (秒)。 如果未設定逾時，它會預設為 30 秒。

### <a name="response"></a>Response
後端應用程式會收到一個由下列各項組成的回應︰

* *HTTP 狀態碼*，用於來自 IoT 中樞的錯誤，包括裝置目前未連接的 404 錯誤
* *標頭* - 包含 ETag、要求識別碼、內容類型及內容編碼
* JSON *本文*格式如下︰

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` 和 `body` 都是由裝置提供，用來回應裝置本身的狀態碼和/或描述。

## <a name="handle-a-direct-method-on-a-device"></a>在裝置上處理直接方法
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>方法引動過程
裝置會接收 MQTT 主題的直接方法要求︰`$iothub/methods/POST/{method name}/?$rid={request id}`

裝置所接收的本文格式如下︰

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

方法要求為 QoS 0。

#### <a name="response"></a>Response
裝置會傳送回應至 `$iothub/methods/res/{status}/?$rid={request id}`，其中︰

* `status` 屬性是方法執行的裝置提供狀態。
* `$rid` 屬性是從 IoT 中樞接收的方法引動過程的要求識別碼。

本文是由裝置設定，而且可以是任何狀態。

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>方法引動過程
裝置會藉由在 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 位址上建立接收連結來接收直接方法要求

AMQP 訊息會送達代表方法要求的接收連結。 其中包含下列項目：
* 相互關聯識別碼屬性，內含應該與對應方法回應一起傳回的要求識別碼
* 名為 `IoThub-methodname` 的應用程式屬性，內含要叫用的方法名稱
* AMQP 訊息內文，內含 JSON 形式的方法承載

#### <a name="response"></a>Response
裝置會建立傳送連結，以在 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 位址上傳回方法回應

此方法的回應會在傳送連結上傳回，且結構如下：
* 相互關聯識別碼屬性，內含方法的要求訊息中所傳遞的要求識別碼
* 名為 `IoThub-status` 的應用程式屬性，內含使用者提供的方法狀態
* AMQP 訊息內文，內含 JSON 形式的方法回應

## <a name="additional-reference-material"></a>其他參考資料
IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-endpoints]說明每個 IoT 中樞公開給執行階段和管理作業的各種端點。
* [節流和配額][lnk-quotas]說明使用「IoT 中樞」時，適用的配額及所預期的節流行為。
* [Azure IoT 裝置和服務 SDK][lnk-sdks] 列出各種語言 SDK，可供您在開發與「IoT 中樞」互動的裝置和服務應用程式時使用。
* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][lnk-query]說明可用於從 IoT 中樞擷取有關裝置對應項和作業資訊的 IoT 中樞查詢語言。
* [IoT 中樞 MQTT 支援][lnk-devguide-mqtt]針對 MQTT 通訊協定提供 IoT 中樞支援的詳細資訊。

## <a name="next-steps"></a>後續步驟
現在您已了解如何使用直接方法，接下來您可能對下列「IoT 中樞」開發人員指南文章感興趣︰

* [排程多個裝置上的作業][lnk-devguide-jobs]

如果您想要嘗試本文章所述的概念，您可能會對下列 IoT 中樞教學課程感興趣：

* [使用直接方法][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
