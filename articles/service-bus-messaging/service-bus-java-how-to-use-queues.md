---
title: "如何搭配使用服務匯流排佇列與 Java | Microsoft Docs"
description: "了解如何使用 Azure 中的服務匯流排佇列。 程式碼範例以 Java 撰寫。"
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 29cab1dff7ffc0f42ee8c605e3817b855967eb53


---
# <a name="how-to-use-service-bus-queues"></a>如何使用服務匯流排佇列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 相關範例是以 Java 撰寫並使用 [Azure SDK for Java][Azure SDK for Java]。 本文說明的案例包括**建立佇列**、**傳送並接收訊息**，以及**刪除佇列**。

## <a name="what-are-service-bus-queues"></a>什麼是服務匯流排佇列？
服務匯流排佇列支援 **代理傳訊** 通訊模型。 使用佇列時，分散式應用程式的元件彼此不直接通訊，相反的，他們會透過扮演中繼角色 (代理人) 的佇列來交換訊息。 訊息產生者 (傳送者) 會將訊息遞交給佇列，然後繼續其處理工作。
訊息取用者 (接收者) 非同步地從佇列中提取訊息並處理。 產生者不必等待取用者的回覆，即可繼續處理及傳送其他訊息。 如果有一或多個競爭取用者，佇列會採取 **先進先出 (FIFO)** 訊息傳遞機制。 亦即，通常由接收者依訊息加入佇列的順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。

![佇列概念](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

服務匯流排佇列為適用於各種情況的通用技術：

* 多層式 Azure 應用程式中 Web 角色和背景工作角色之間的通訊。
* 混合式解決方案中的內部部署應用程式和 Azure 代管應用程式之間的通訊。
* 在不同組織或同一組織的不同部門中，在內部部署執行之分散式應用程式的各元件之間的通訊。

使用佇列，可讓應用程式更容易地進一步向外延展，並啟用架構的備援能力。

## <a name="create-a-service-namespace"></a>建立服務命名空間
若要開始在 Azure 中使用服務匯流排佇列，必須先建立命名空間。 命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。

若要建立命名空間：

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>設定應用程式以使用服務匯流排
先確定已安裝 [Azure SDK for Java][Azure SDK for Java] 再建置此範例。 如果使用 Eclipse，您可以安裝包含 Azure SDK for Java 的 [適用於 Eclipse 的 Azure 工具組][適用於 Eclipse 的 Azure 工具組]。 然後您可以將 **Microsoft Azure Libraries for Java** 新增至您的專案：

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

在 Java 檔案頂端新增下列 `import` 陳述式：

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>建立佇列
可以透過 **ServiceBusContract** 類別，來執行服務匯流排佇列的管理作業。 **ServiceBusContract** 物件是利用使用權限來封裝 SAS 權杖以加以管理的適當組態所建構，而 **ServiceBusContract** 類別是唯一可與 Azure 通訊的點。

**ServiceBusService** 類別提供建立、列舉及刪除佇列的方法。 以下範例顯示如何使用 **ServiceBusService** 物件建立名稱為「TestQueue」的佇列及名稱為「HowToSample」的命名空間：

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

**QueueInfo** 有相關方法可讓您調整佇列的屬性 (例如，針對要在傳送至佇列的訊息所套用的存留時間 (TTL) 設定預設值)。 下列範例示範如何使用大小上限為 5 GB 的設定，來建立名為 `TestQueue` 的佇列：

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

請注意，您可以在 **ServiceBusContract** 物件上使用 **listQueues** 方法，來檢查服務命名空間內是否已有指定名稱的佇列存在。

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
若要將訊息傳送至服務匯流排佇列，應用程式會取得 **ServiceBusContract** 物件。 下列程式碼示範如何針對先前在 `HowToSample` 命名空間中建立的 `TestQueue` 佇列傳送訊息：

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

傳送至 (和擷取自) 服務匯流排佇列的訊息是 [BrokeredMessage][BrokeredMessage] 類別的執行個體。 [BrokeredMessage][BrokeredMessage] 物件具有一組標準屬性 (例如 [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))、一個用來保存自訂應用程式特定屬性的目錄，以及一堆任意的應用程式資料。 應用程式可設定訊息主體，方法是將任何可序列化物件傳遞到 [BrokeredMessage][BrokeredMessage] 的建構函式，接著系統便會使用適當的序列化程式來序列化物件。 此外，您也可以提供 **java.IO.InputStream** 物件。

下列範例示範如何將五則測試訊息傳送至上述程式碼片段中所取得的 `TestQueue` **MessageSender**：

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

服務匯流排佇列支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 標頭 (包含標準和自訂應用程式屬性) 可以容納 64 KB 的大小上限。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 此佇列大小會在建立時定義，上限是 5 GB。

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
自佇列接收訊息的主要方式是使用 **ServiceBusContract** 物件。 接收的訊息可在兩種不同的模式下運作：**ReceiveAndDelete** 和 **PeekLock**。

使用 **ReceiveAndDelete** 模式時，接收是一次性作業；也就是說，當服務匯流排在佇列中收到訊息的讀取要求時，它會將此訊息標示為已使用，並將它傳回應用程式。 **ReceiveAndDelete** 模式 (此為預設模式) 是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。
因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它會在已接收的訊息上呼叫 **Delete**，以完成接收程序的第二個階段。 當服務匯流排看到 **Delete** 呼叫時，它會將訊息標示為已取用，並將它從佇列中移除。

以下範例示範如何使用 **PeekLock** 模式 (非預設模式) 來接收與處理訊息。 下列範例會建立一個無限迴圈，並在訊息抵達 "TestQueue" 時處理訊息：

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                 service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機與無法讀取的訊息
服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因無法處理訊息，它可以在已接收的訊息上呼叫 **unlockMessage** 方法 (而不是 **deleteMessage** 方法)。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排將自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未發出 **deleteMessage** 要求時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 通常您可使用訊息的 **getMessageId** 方法來達到此目的，該方法將在各個傳遞嘗試中保持不變。

## <a name="next-steps"></a>後續步驟
現在您已了解服務匯流排佇列的基本概念，請參閱[佇列、主題和訂用帳戶][佇列、主題和訂用帳戶]，以取得詳細資訊。

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[適用於 Eclipse 的 Azure 工具組]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[佇列、主題和訂用帳戶]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx




<!--HONumber=Nov16_HO3-->


