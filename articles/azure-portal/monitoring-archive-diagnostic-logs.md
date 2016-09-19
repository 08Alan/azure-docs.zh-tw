<properties
	pageTitle="封存 Azure 診斷記錄 | Microsoft Azure"
	description="了解如何封存 Azure 診斷記錄以在儲存體帳戶中長期保存。"
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="johnkem"/>

# 封存 Azure 診斷記錄
在本文中，我們會示範如何使用 Azure 入口網站、PowerShell Cmdlet、CLI 或 REST API 來封存儲存體帳戶中的 [Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)。如果您想要使用適用於稽核、靜態分析或備份的選用保留原則來保留診斷記錄，這個選項非常有用。

## 必要條件
在開始之前，您需要[建立儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)，以便將診斷記錄封存至此。我們強烈建議您不要使用已儲存了其他非監視資料的現有儲存體帳戶，這樣您對監視資料才能有更好的存取控制。不過，如果您也要封存活動記錄和診斷度量至儲存體帳戶，則將同一儲存體帳戶用於診斷記錄合情合理，因為可以將所有監視資料集中在一個位置。您使用的儲存體帳戶必須是一般用途的儲存體帳戶，不可以是 blob 儲存體帳戶。

## 診斷設定
若要使用下列任何方法封存診斷記錄，您必須為特定資源設定**診斷設定**。資源的診斷設定會定義所儲存或串流的記錄類別以及輸出 — 儲存體帳戶及/或事件中樞。它也會定義儲存在儲存體帳戶中每個記錄類別之事件的保留原則 (保留的天數)。如果保留原則設定為零，則會無限期地 (亦即永遠) 儲存該記錄類別的事件。否則，保留原則可以是 1 到 2147483647 之間的任何天數。[您可以在此深入了解診斷設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。

## 使用入口網站封存診斷記錄

1. 在入口網站中，按一下要對其啟用診斷記錄封存之資源的 [資源] 刀鋒視窗。
2. 在資源設定功能表的 [監視] 區段選取 [診斷]。

    ![資源功能表的監視區段](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. 勾選 [匯出到儲存體帳戶] 核取方塊，然後選取儲存體帳戶。使用 [保留 (天)] 滑桿選擇性地設定這些記錄的保留天數。保留天數為 0 會無限期地儲存記錄。

	![診斷記錄刀鋒視窗](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. 按一下 [儲存]。

新事件資料產生之後，診斷記錄就會立即封存至該儲存體帳戶。

## 透過 PowerShell Cmdlet 封存診斷記錄

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| 屬性 | 必要 | 說明 |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId | 是 | 要對其設定診斷設定之資源的資源識別碼。 |
| StorageAccountId | 否 | 資源識別碼，診斷記錄應該要儲存至此儲存體帳戶。 |
| 類別 | 否 | 要啟用之記錄類別的逗號分隔清單。 |
| 已啟用 | 是 | 布林值，表示要對資源啟用還是停用診斷。 |
| RetentionEnabled | 否 | 布林值，表示此資源是否啟用保留原則。 |
| RetentionInDays | 否 | 事件應保留的天數，1 到 2147483647 之間。值為 0 會無限期地儲存記錄檔。 |

## 透過跨平台 CLI 封存活動記錄

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| 屬性 | 必要 | 說明 |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId | 是 | 要對其設定診斷設定之資源的資源識別碼。 |
| storageId | 否 | 資源識別碼，診斷記錄應該要儲存至此儲存體帳戶。 |
| categories | 否 | 要啟用之記錄類別的逗號分隔清單。 |
| 啟用 | 是 | 布林值，表示要對資源啟用還是停用診斷。 |

## 透過 REST API 封存診斷記錄
[請參閱本文件](https://msdn.microsoft.com/library/azure/dn931931.aspx)，以取得如何使用 Insights REST API 設定診斷設定的資訊。

## 儲存體帳戶中的診斷記錄結構描述
設定了封存之後，便會在已啟用的其中一個記錄類別發生事件時，立即於儲存體帳戶中建立儲存體容器。容器內的 Blob 的診斷記錄和活動記錄會遵循相同的格式。這些 blob 的結構為：

> insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

或者，形式更簡單，

> insights-logs-{log category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

例如，blob 名稱可能是︰

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。分鐘值 (m = 00) 一定是 00，因為診斷記錄事件是分成每小時的個別 blob。

在 PT1H.json 檔案中，每個事件會以下列格式儲存在 “records” 陣列︰

```
{
	"records": [
		{
			"time": "2016-07-01T00:00:37.2040000Z",
			"systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
			"category": "NetworkSecurityGroupRuleCounter",
			"resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
			"operationName": "NetworkSecurityGroupCounters",
			"properties": {
				"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
				"subnetPrefix": "10.3.0.0/24",
				"macAddress": "000123456789",
				"ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
				"direction": "In",
				"type": "allow",
				"matchedConnections": 1988
			}
		}
	]
}
```

| 元素名稱 | 說明 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| 分析 | 處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| resourceId | 受影響資源的資源識別碼。 |
| operationName | 作業名稱。 |
| category | 事件的記錄類別。 |
| properties | 描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |

> [AZURE.NOTE] 屬性和這些屬性的使用方式，依資源而異。

## 後續步驟
- [下載 blob 以供分析](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [將診斷記錄串流至事件中樞](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [深入了解診斷記錄](monitoring-overview-of-diagnostic-logs.md)

<!----HONumber=AcomDC_0907_2016-->