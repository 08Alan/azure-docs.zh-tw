<properties 
    pageTitle="服務匯流排傳訊例外狀況 | Microsoft Azure"
    description="服務匯流排傳訊例外狀況和建議的動作清單。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/02/2016"
    ms.author="sethm" />

# 服務匯流排傳訊例外狀況

本文列出一些 Microsoft Azure 服務匯流排傳訊 API 產生的例外狀況。此參考可能有所變更，請不定期查看更新。

## 例外狀況類別

傳訊 API 會產生下列類別的例外狀況，以及可用來嘗試修正它們的相關動作。請注意，例外狀況的意義和原因會隨著傳訊實體 (轉送、佇列/主題、事件中樞) 的類型而異︰

1.  使用者程式碼撰寫錯誤 ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)、[System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)、[System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)、[System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx))。一般動作：請先嘗試修正此程式碼，再繼續執行。

2.  設定/組態錯誤 ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx)、[Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)。一般動作：檢閱您的組態並視需要進行變更。

3.  暫時性例外狀況 ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx)、[Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx)、[Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx))。一般動作：重試此操作或通知使用者。

4.  其他例外狀況 ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx)、[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)、[Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx)、[Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx))。一般動作︰依例外狀況類型而異；請參閱下一節中的表格。

## 例外狀況類型

下表列出傳訊例外狀況類型及其原因，並指出您可以採取的建議動作。

| **例外狀況類型** | **描述/原因/範例** | **建議的動作** | **自動/立即重試附註** |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) | 伺服器未在 [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) 控制的指定時間內回應要求的作業。伺服器可能已完成要求的作業。這可能是由於網路或其他基礎結構延遲所導致。 | 檢查系統狀態的一致性，並視需要重試。 | 在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) | 不允許在伺服器或服務內執行要求的使用者作業。如需詳細資訊，請參閱例外狀況訊息。例如，如果是在 **ReceiveAndDelete** 模式收到訊息，[Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 將會產生這個例外狀況。 | 檢查程式碼和文件。確定要求的作業無效。 | 重試將無助益。 |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | 嘗試在已關閉、中止或處置的物件上叫用作業。極少數的情況下，環境交易是已處置狀態。 | 檢查程式碼，確定它不會在已處置物件上叫用作業。 | 重試將無助益。 |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 物件無法取得權杖、權杖無效，或權杖不包含執行作業所需的宣告。 | 確定權杖提供者是以正確的值建立。檢查存取控制服務的組態。 | 在某些情況下，重試也許有幫助；將重試邏輯新增至程式碼。 |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | 提供給方法的一或多個引數無效。提供給 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 或 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) 的 URI 包含路徑區段。提供給 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 或 [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) 的 URI 配置無效。屬性值大於 32 KB。 | 檢查呼叫程式碼，並確定引數正確無誤。 | 重試將無助益。 |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) | 與作業相關聯的實體不存在或已被刪除。 | 確定實體已存在。 | 重試將無助益。 |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) | 嘗試接收具有特定序號的訊息。找不到此訊息。 | 確定尚未收到訊息。檢查寄不出信件佇列，查看訊息是否已停止傳送。 | 重試將無助益。 |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) | 用戶端無法建立服務匯流排連線。 | 確定提供的主機名稱正確，且主機可以連線。 | 如果有間歇性的連線問題，重試也許有幫助。 |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) | 服務目前無法處理要求。 | 用戶端可以等待一段時間，然後再重試作業。 | 用戶端可以在特定間隔後重試。如果重試產生不同的例外狀況，請檢查該例外狀況的重試行為。 |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) | 與訊息相關的鎖定權杖已過期，或找不到鎖定權杖。 | 處置訊息。 | 重試將無助益。 |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) | 與此工作階段相關聯的鎖定遺失。 | 中止 [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx) 物件。 | 重試將無助益。 |
| [MessagingException 類別](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) | 可能會在下列情況中擲回的一般傳訊例外狀況：利用屬於不同實體類型 (例如主題) 的名稱或路徑嘗試建立 [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)。嘗試傳送大於 256 KB 的訊息。處理要求時伺服器或服務發生錯誤。如需詳細資訊，請參閱例外狀況訊息。這通常是暫時性例外狀況。 | 查看程式碼，並確定訊息內文只使用可序列化的物件 (或使用自訂序列化程式)。查看文件來了解支援的屬性值類型，並且只使用支援的類型。查看 [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx) 屬性。如果該屬性為 **True**，您就可以重試作業。 | 重試行為未定義，而且可能沒有幫助。 |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) | 嘗試在該服務命名空間中以另一個實體已在使用的名稱建立實體。 | 刪除現有的實體，或選擇不同的名稱來建立實體。 | 重試將無助益。 |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) | 傳訊實體已達到允許的大小上限。 | 從實體或其子佇列接收訊息，在實體中建立空間。 | 如果在此同時已移除訊息，重試可能會有幫助。 |
| [RuleActionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruleactionexception.aspx) | 如果您嘗試建立無效的規則動作，服務匯流排會傳回此例外狀況。如果處理停止傳送的訊息的規則動作時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 | 檢查規則動作的正確性。 | 重試將無助益。 |
| [FilterException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.filterexception.aspx) | 如果您嘗試建立無效的篩選，服務匯流排會傳回此例外狀況。如果處理停止傳送的訊息的篩選時發生錯誤，服務匯流排會將此例外狀況附加至該訊息。 | 檢查篩選的正確性。 | 重試將無助益。 |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) | 嘗試接受含有特定工作階段識別碼的工作階段，但該工作階段目前被另一個用戶端鎖定。 | 確定其他用戶端已解除鎖定工作階段。 | 如果工作階段在過渡期間被解除鎖定，重試可能會有幫助。 |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) | 交易包含太多作業。 | 減少此交易的作業數目。 | 重試將無助益。 |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) | 在停用的實體上要求執行階段作業。 | 啟用實體。 | 如實體在過渡期間被啟用，重試可能會有幫助。 |
| [NoMatchingSubscriptionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.nomatchingsubscriptionexception.aspx) | 如果您將訊息傳送至已啟用預先篩選的主題，但沒有符合的篩選，服務匯流排就會傳回此例外狀況。 | 確定至少有一個篩選相符。 | 重試將無助益。 |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) | 訊息裝載超過 256K 的限制。請注意，256k 的限制是總訊息大小，可包括系統屬性和任何 .NET 負荷。 | 減少訊息裝載大小，然後再重試作業。 | 重試將無助益。 |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) | 環境交易 (*Transaction.Current*) 無效。其可能已完成或中止。內部例外狀況可能會提供其他資訊。 | | 重試並沒有任何幫助。 | - | [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) | 嘗試對不確定的交易執行作業，或是嘗試認可交易，但交易變成不確定。 | 應用程式必須處理這個例外狀況 (當成特殊狀況)，因為交易可能已遭認可。 | - |

## QuotaExceededException

[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) 指出已超過某特定實體的配額。

### 佇列和主題

對佇列和主題而言，這通常是佇列的大小。錯誤訊息屬性會包含進一步的詳細資訊，如下例所示︰

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
	Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

訊息指出主題超過其大小限制，本例為 1 GB (預設大小限制)。

#### 常見的原因

這個錯誤有兩個常見的原因︰無效信件佇列和訊息接收者未作用。

1. **無效信件佇列**：讀取器無法完成訊息，當鎖定過期後，訊息會傳回佇列/主題。如果讀取器遇到例外狀況，阻止它呼叫 [BrokeredMessage.Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)，就會發生這個錯誤。訊息讀取 10 次後，預設會移至無效信件佇列。這種行為由 [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) 屬性控制，預設值是 10。訊息堆積在無效信件佇列中會佔用空間。

	若要解決此問題，請閱讀和完成無效信件佇列中的訊息，就像您處理任何其他佇列一樣。[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 類別甚至包含 [FormatDeadLetterPath](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.formatdeadletterpath.aspx) 方法，可協助格式化無效信件佇列路徑。

2. **收件者停止**：收件者停止接收佇列或訂用帳戶的訊息。識別這個原因的方法是查看 [QueueDescription.MessageCountDetails](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecountdetails.aspx) 屬性，它會顯示訊息的完整解析。如果 [ActiveMessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagecountdetails.activemessagecount.aspx) 屬性很高或不斷增加，表示訊息撰寫的速度超過讀取的速度。

### 事件中樞

每一個事件中樞都有 20 個用戶群組的限制。當您嘗試建立更多時，您會收到 [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx)。

### 轉送

若為服務匯流排轉送，這個例外狀況會包裝 [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx)，指出已超過這個端點接聽程式的最大數目。這會表示在例外狀況訊息的 **MaximumListenersPerEndpoint** 值中。

## TimeoutException 

[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) 表示使用者啟始作業所用的時間長過作業逾時。

### 佇列和主題

佇列和主題的逾時是在 [MessagingFactorySettings.OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) 屬性中指定，作為連接字串的一部分，或透過 [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx) 指定。錯誤訊息本身可能不盡相同，但它一定會包含目前作業的指定逾時值。

### 事件中樞

事件中樞的逾時是指定為連接字串的一部分，或透過 [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx) 指定。錯誤訊息本身可能不盡相同，但它一定會包含目前作業的指定逾時值。

### 轉送

若為服務匯流排轉送，您可能會在第一次開啟轉送傳送者連線時收到逾時例外狀況。這個例外狀況有兩個常見的原因︰

1. [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 值可能太小 (甚至幾分之一秒)。
2. 內部部署轉送接聽程式可能沒有回應 (或可能遇到禁止接聽程式接受新用戶端連線的防火牆規則問題)，而 [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) 值約小於 20 秒。

例如：

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### 常見的原因

這個錯誤有兩個常見的原因︰設定不正確或暫時性服務錯誤。

1. **設定不正確**：操作條件的作業逾時可能太小。用戶端 SDK 的作業逾時預設值為 60 秒。請檢查程式碼是否將值設定過小。請注意，網路和 CPU 使用量的狀況會影響特定作業完成所花費的時間，所以作業逾時不應設定非常小的值。

2. **暫時性服務錯誤**：有時服務匯流排服務在處理要求時會遇到延遲，例如，高流量的時段。在這種情況下，您可以在延遲後重試作業，直到作業成功為止。如果多次嘗試同一作業之後仍然失敗，請瀏覽 [Azure 服務狀態網站](https://azure.microsoft.com/status/)，看看是否有任何已知的服務中斷。

## 後續步驟

如需完整的服務匯流排和事件中樞 .NET API 參考資料，請參閱 [MSDN 上的 Azure 參考資料](https://msdn.microsoft.com/library/azure/mt419900.aspx)。

若要深入了解[服務匯流排](https://azure.microsoft.com/services/service-bus/)，請參閱下列主題。

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
- [服務匯流排架構](service-bus-architecture.md)

<!---HONumber=AcomDC_0518_2016-->