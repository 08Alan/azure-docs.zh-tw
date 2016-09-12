<properties 
	pageTitle="如何監視 Azure Redis 快取 | Microsoft Azure" 
	description="了解如何監視您 Azure Redis 快取執行個體的健全狀況和效能" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="sdanie"/>

# 如何監視 Azure Redis 快取

Azure Redis 快取提供數個選項來監視您的快取執行個體。您可以檢視度量、將度量圖表釘選到「開始面板」、自訂監視圖表的日期和時間範圍、新增和移除圖表中的度量，以及設定符合特定條件時的警示。這些工具可讓您監視 Azure Redis 快取執行個體的健全狀況，並協助您管理快取應用程式。

啟用快取診斷時，大約每 30 秒會收集並儲存一次 Azure Redis 快取執行個體的度量，以將它們顯示在度量圖中，並透過警示規則進行評估。

快取度量是使用 Redis [INFO](http://redis.io/commands/info) 命令進行收集。如需用於每個快取度量的不同 INFO 值詳細資訊，請參閱[可用度量和報告間隔](#available-metrics-and-reporting-intervals)。

若要檢視快取度量，請[瀏覽](cache-configure.md#configure-redis-cache-settings)至您在 [Azure 入口網站](https://portal.azure.com)中的快取執行個體。您可以在 [Redis 快取] 刀鋒視窗上存取 Azure Redis 快取執行個體的度量。

![Redis 度量][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] 如果 [Redis 度量] 刀鋒視窗上顯示了下列訊息，請遵循[啟用快取診斷](#enable-cache-diagnostics)一節中的步驟來啟用快取診斷。
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

[Redis 度量] 刀鋒視窗中的 [監視] 圖表會顯示快取度量。新增或移除度量，以及變更報告間隔，即可自訂每個圖表。為了檢視和設定作業和警示，[Redis 快取] 刀鋒視窗提供 [作業] 區段，來顯示快取**事件**和**警示規則**。

## 啟用快取診斷

Azure Redis 快取可讓您將診斷資料儲存在儲存體帳戶中，因此，您可以使用任何想要存取的工具，並直接處理資料。若要在 Azure 入口網站中收集、儲存和顯示快取診斷，必須設定儲存體帳戶。相同區域和訂用帳戶中的快取會共用相同的診斷儲存體帳戶，在組態變更時，會套用至訂用帳戶中所有位於該區域的快取。

若要啟用和設定快取診斷，請瀏覽至您快取執行個體的 [Redis 快取] 分頁。如果尚未啟用診斷，則會顯示訊息，而非診斷圖表。

![啟用快取診斷][redis-cache-enable-diagnostics]

按一下訊息來顯示 [度量] 刀鋒視窗，然後按一下 [診斷設定] 來啟用和設定快取服務執行個體的診斷設定。

![診斷設定][redis-cache-diagnostic-settings]

![設定診斷][redis-cache-configure-diagnostics]

按一下 [開啟] 按鈕啟用快取診斷，並顯示診斷組態。

按一下 [儲存體帳戶] 右邊的箭號，來選取要保存診斷資料的儲存體帳戶。為了達到最佳效能，請在與您快取相同的區域中選取儲存體帳戶。

設定診斷設定之後，請按一下 [儲存] 儲存組態。請注意，可能需要幾分鐘的時間，變更才會生效。

>[AZURE.IMPORTANT] 相同區域和訂用帳戶中的快取會共用相同的診斷儲存體設定，在組態變更時 (啟用/停用診斷或變更儲存體帳戶)，會套用至訂用帳戶中所有位於該區域的快取。

若要檢視預存度量，請檢查儲存體帳戶中名稱開頭為 `WADMetrics` 的資料表。如需在 Azure 入口網站外部存取預存度量的詳細資訊，請參閱[存取 Redis 快取監視資料](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)範例。

>[AZURE.NOTE] 只有儲存在所選取儲存體帳戶中的度量才會顯示在 Azure 入口網站中。如果您變更儲存體帳戶，則先前設定之儲存體帳戶中的資料仍然可供下載，但不會顯示在 Azure 入口網站中。

## 可用度量和報告間隔

快取度量是使用數個報告間隔所報告，包括 [過去一小時]、[今天]、[過去一週] 和 [自訂]。每個度量圖的 [度量] 分頁都會顯示圖表中每個度量的平均值、最小值和最大值，而有些度量會顯示報告間隔的總計。

每個度量都包含兩個版本。有一個度量會測量整個快取的效能，以及使用[叢集](cache-how-to-premium-clustering.md)之快取的效能，名稱中包含 `(Shard 0-9)` 的第二個度量版本則會測量快取中單一分區的效能。例如，如果快取有 4 個分區，`Cache Hits` 就是整個快取的點擊總數，而 `Cache Hits (Shard 3)` 就只是該快取分區的點擊數。

>[AZURE.NOTE] 即使快取閒置而沒有連線的作用中用戶端應用程式，您可能還是會看到某個快取活動 (例如連線的用戶端、記憶體使用量，以及正在執行的作業)。在 Azure Redis 快取執行個體的作業期間，此活動正常。

| 度量 | 說明 |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 快取點擊 | 所指定報告間隔期間的成功金鑰查閱數目。這會對應至 Redis [INFO](http://redis.io/commands/info) 命令的 `keyspace_hits`。 |
| 快取遺漏 | 所指定報告間隔期間的失敗金鑰查閱數目。這會對應至 Redis INFO 命令的 `keyspace_misses`。快取遺漏不一定表示快取發生問題。例如，使用另行快取程式設計模式時，應用程式會先在快取中尋找項目。如果項目不存在 (快取遺漏)，項目會從資料庫中擷取，並在下次新增至快取中。快取遺漏是另行快取程式設計模式的正常行為。如果快取遺漏數目高於預期，請檢查可填入且讀取自快取的應用程式邏輯。如果因記憶體壓力而正在收回快取中的項目，則可能會有一些快取遺漏，但監視記憶體壓力的較佳度量是 `Used Memory` 或 `Evicted Keys`。 |
| 連線的用戶端 | 所指定報告間隔期間的快取用戶端連線數目。這會對應至 Redis INFO 命令的 `connected_clients`。一旦達到[連接限制](cache-configure.md#default-redis-server-configuration)之後，後續對快取所做的連接嘗試都將失敗。請注意，因為內部處理序和連線，所以即使沒有作用中用戶端應用程式，可能還是會有一些連線的用戶端執行個體。 |
| 收回的金鑰 | 因 `maxmemory` 限制，在所指定報告間隔期間從快取收回的項目數。這會對應至 Redis INFO 命令的 `evicted_keys`。 |
| 到期的金鑰 | 所指定報告間隔期間的快取到期項目數。這個值會對應至 Redis INFO 命令的 `expired_keys`。 |
| 取得 | 所指定報告間隔期間的快取 get 作業數目。這個值是 Redis INFO all 命令的下列值總和：`cmdstat_get`、`cmdstat_hget``cmdstat_hgetall`、`cmdstat_hmget``cmdstat_mget`、`cmdstat_getbit` 和 `cmdstat_getrange`，而且等於報告期間的快取點擊和遺漏。 |
| Redis 伺服器負載 | Redis 伺服器忙著處理訊息且非訊息等候閒置之循環的百分比。如果這個計數器達到 100，表示 Redis 伺服器已經達到效能上限，而且 CPU 處理工作的速度不能再更快。如果您看到高「Redis 伺服器負載 (Redis Server Load)」，則會看到用戶端中的逾時例外狀況。在此情況下，您應該考慮向上延展，或將資料分割成多個快取。 |
| 設定 | 所指定報告間隔期間的快取 set 作業數目。這個值是 Redis INFO all 命令的下列值總和：`cmdstat_set`、`cmdstat_hset``cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange` 和 `cmdstat_setnx` |
| 總作業數 | 所指定報告間隔期間，快取伺服器所處理命令的總數。這個值會對應至 Redis INFO 命令的 `total_commands_processed`。請注意，Azure Redis 快取純粹用於發佈/訂閱時，則沒有 `Cache Hits`、`Cache Misses`、`Gets` 或 `Sets` 的度量但是會有 `Total Operations` 度量可反映發佈/訂閱作業的快取使用量。 |
| 已使用的記憶體 | 在指定的報告間隔期間，針對快取中金鑰/值組使用的快取記憶體數量 (MB)。這個值會對應至 Redis INFO 命令的 `used_memory`。這不包括中繼資料或片段。 |
| 已用的記憶體 RSS | 在指定的報告間隔期間使用的快取記憶體數量 (MB)，包括片段和中繼資料。這個值會對應至 Redis INFO 命令的 `used_memory_rss`。 |
| CPU | 所指定報告間隔期間的 Azure Redis 快取伺服器 CPU 使用率 (百分比)。這個值會對應至作業系統 `\Processor(_Total)\% Processor Time` 效能計數器。 |
| 快取讀取 | 所指定報告間隔期間，從快取讀取的資料量 (以 MB/s 為單位)。這個值衍生自網路介面卡，而網路介面卡支援裝載快取且非 Redis 特有的虛擬機器。**這個值對應於此快取所使用的網路頻寬。如果您想要設定伺服器端網路頻寬限制的警示，則可使用此 `Cache Read` 計數器加以建立。如需所觀察到適用於各種快取定價層和大小的頻寬限制，請參閱[這個表格](cache-faq.md#cache-performance)。** |
| 快取寫入 | 所指定報告間隔期間，寫入至快取的資料量 (以 MB/s 為單位)。這個值衍生自網路介面卡，而網路介面卡支援裝載快取且非 Redis 特有的虛擬機器。此值對應從用戶端傳送給快取之資料的網路頻寬。 |


## 如何檢視度量以及自訂圖表

您可以在 [Redis 度量] 刀鋒視窗上顯示快取的度量概觀。若要存取 [Redis 度量] 刀鋒視窗，可選擇 [所有設定] > [Redis 度量]。

![Redis 度量][redis-cache-redis-metrics]


[Redis 度量] 刀鋒視窗包含下列圖表。

| Redis 度量圖表 | 顯示的度量 |
|------------------|-------------------|
| 快取讀取和快取寫入 | 快取讀取 |
| | 快取寫入 |
| 連線的用戶端 | 連線的用戶端 |
| 點擊和遺漏 | 快取點擊 |
| | 快取遺漏 |
| 總命令數 | 總作業數 |
| 取得和設定 | 取得 |
| | 設定 |
| CPU 使用率 | CPU |
| 記憶體使用量 | 已使用的記憶體 |
| | 已用的記憶體 RSS |
| Redis 伺服器負載 | 伺服器負載 |
| 索引鍵計數 | 索引鍵總計 |
| | 收回的金鑰 |
| | 到期的金鑰 |


如需特定圖表上度量的更詳細檢視以及自訂圖表，按一下 [Redis 快取] 刀鋒視窗中所需的圖表，以顯示該圖表的 [度量] 刀鋒視窗。

![Metric blade][redis-cache-metric-blade]

圖表所顯示度量上設定的任何警示都會列在該圖表的 [度量] 分頁底部。

若要新增或移除度量，或是變更報告間隔，可選擇 [編輯圖表]。

若要新增或移除圖表中的度量，請按一下度量名稱旁邊的核取方塊。若要變更報告間隔，請按一下想要的間隔。若要變更 [圖表類型]，請按一下想要的樣式。進行想要的變更之後，請按一下 [儲存]。

![編輯圖表][redis-cache-edit-chart]

如果您按一下 [儲存]，您的變更將會保留到您退出 [度量] 分頁為止。當您後續返回頁面時，您將會看見原有的度量和時間範圍。如需自訂圖表的詳細資訊，請參閱[監視服務度量](../azure-portal/insights-how-to-customize-monitoring.md)。

若要檢視圖表上一段特定時間的度量，請將滑鼠放在圖表上對應至想要時間的其中一個特定橫條或點，並顯示該間隔的度量。

![檢視圖表詳細資料][redis-cache-view-chart-details]

## 如何監視含叢集的進階快取

已啟用[叢集](cache-how-to-premium-clustering.md)的進階快取可以具有最多 10 個分區。每個分區都有它自己的度量，而且這些度量會彙總提供整個度量供快取。每個度量都包含兩個版本。有一個度量會測量整個快取的效能，名稱中包含 `(Shard 0-9)` 的第二個度量版本則會測量快取中單一分區的效能。例如，如果快取有 3 個分區，`Cache Hits` 就是整個快取的點擊總數，而 `Cache Hits (Shard 2)` 就只是該快取分區的點擊數。

每張監視圖表會顯示每個快取的最上層度量以及每個快取分區的度量。

![監視][redis-cache-premium-monitor]

將滑鼠停留在資料點上會顯示該時間點的詳細資料。

![監視][redis-cache-premium-point-summary]

較大的值通常是快取的彙總值，而較小的值是分區的個別度量。請注意，在此範例中有三個分區，快取點擊平均分散至分區。

![監視][redis-cache-premium-point-shard]

若要查看更詳細的資訊，可按一下圖表在 [度量] 刀鋒視窗上檢視展開的檢視。

![監視][redis-cache-premium-chart-detail]

依預設，每個圖表會包含最上層的快取效能計數器以及個別分區的效能計數器。您也可以在 [編輯圖表] 刀鋒視窗上自訂這些。

![監視][redis-cache-premium-edit]

如需有關可用的效能計數器的詳細資訊，請參閱[可用的度量和報告間隔](#available-metrics-and-reporting-intervals)。


## 作業和警示

[Redis 快取] 刀鋒視窗上的 [作業] 區段具有 [事件] 和 [警示規則] 區段。

![作業][redis-cache-operations-events]

若要查看最新快取作業的清單，請按一下 [事件] 圖表顯示 [事件] 分頁。作業的範例包括擷取和重新產生存取金鑰，以及警示規則的啟動和解決。如需每個事件的詳細資訊，請按一下 [事件] 分頁中的事件。

如需事件的詳細資訊，請參閱[檢視事件和稽核記錄檔](../azure-portal/insights-debugging-with-events.md)。

[警示規則] 區段會顯示快取執行個體的警示計數。警示規則可讓您監視快取執行個體，並在特定度量值達到規則中所定義的閾值時即收到電子郵件。

大約每五分鐘即評估一次警示規則，並在啟動警示規則時傳送任何設定的通知。不會立即處理警示規則啟動和通知；可能會延遲數分鐘的時間，才會啟動警示規則並傳送通知。

您可以從特定監視圖的 [度量] 分頁或是 [警示規則] 分頁檢視和設定警示規則。

警示規則具有下列屬性。

| 警示規則屬性 | 說明 |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 資源 | 警示規則所評估的資源。從 Redis 快取建立警示規則時，快取是資源。 |
| Name | 可唯一識別目前快取執行個體內警示規則的名稱。 |
| 說明 | 警示規則的選擇性描述。 |
| 度量 | 要由警示規則所監視的度量。如需快取度量清單，請參閱＜可用度量和報告間隔＞。 |
| 條件 | 警示規則的條件運算子。可能的選項為：大於、大於或等於、小於、小於或等於。 |
| 閾值 | 用來使用條件屬性所指定的運算子與度量進行比較的值。根據度量，這個值的單位可能是位元組/秒、位元組、% 或計數。 |
| 期間 | 指定將度量平均值用於警示規則比較的期間。例如，如果期間超過前一個小時，則會將前一個小時間隔的度量平均值用於進行比較。如果您想要在因活動突然增加而符合閾值時收到通知，則較短的期間會較適合。若要在持續活動超出閾值時收到通知，請使用較長的期間。 |
| 電子郵件服務和共同管理員 | 符合時，啟動警示時是否傳送電子郵件給服務管理員和共同管理員。 |
| 其他的管理員電子郵件 | 要在啟動警示時收到通知之其他管理員的選擇性電子郵件地址。 |

一個警示規則啟動只會傳回一個通知。超過規則的閾值並傳送通知之後，除非度量低於閾值，否則不會重新評估規則。如果度量之後超過閾值，則會重新啟動警示，並傳送新的通知。

若要檢視快取執行個體的所有警示規則，請按一下 [Redis 快取] 分頁中的 [警示規則] 部分。若只要檢視使用特定度量的警示規則，請瀏覽含有該度量之圖表的 [度量] 分頁。

![警示規則][redis-cache-alert-rules]

若要新增警示規則，請按一下 [度量] 分頁或 [警示規則] 分頁中的 [新增警示]。

將想要的規則條件輸入至 [新增警示規則] 分頁，然後按一下 [確定]。

![新增警示規則][redis-cache-add-alert]

>[AZURE.NOTE] 按一下 [度量] 分頁中的 [新增警示] 以建立警示規則時，只有該分頁之圖表上顯示的度量才會出現在 [度量] 下拉式清單中。按一下 [警示規則] 分頁中的 [新增警示] 以建立警示規則時，所有快取度量都會出現在 [度量] 下拉式清單中。

警示規則在儲存之後，會出現在 [警示規則] 分頁上，以及顯示警示規則中所使用度量之任何圖表的 [度量] 分頁上。若要編輯警示規則，請按一下要顯示 [編輯規則] 分頁之警示規則的名稱。從 [編輯規則] 分頁中，您可以編輯規則的屬性、刪除或停用警示規則，或重新啟用先前已停用的規則。

>[AZURE.NOTE] 您對規則屬性進行的任何變更可能需要一些時間，才會反映在 [警示規則] 分頁或 [度量] 分頁上。

啟動警示規則時，會根據警示規則的組態傳送電子郵件，並且會在 [Redis 快取] 分頁的 [警示規則] 部分中顯示警示圖示。

警示條件不再評估為 true 時，會將警示規則視為已解決。解決警示規則條件之後，會將警示圖示取代為核取記號。如需警示啟動和解決方法的詳細資料，請按一下 [Redis 快取] 分頁的 [事件] 部分，以檢視 [事件] 分頁上的事件。

如需 Azure 中警示的詳細資訊，請參閱[接收警示通知](../azure-portal/insights-receive-alert-notifications.md)。

## [Redis 快取] 刀鋒視窗上的度量

[Redis 快取] 刀鋒視窗會顯示下列度量類別。

-	[監視圖表](#monitoring-charts)
-	[使用量圖表](#usage-charts)

### 監視圖表

[監視] 區段具有 [點擊和遺漏]、[取得和設定]、[連線] 和 [總命令數] 圖表。

![監視圖表][redis-cache-monitoring-part]

[監視] 圖表會顯示下列度量。

| 監視圖表 | 快取度量 |
|------------------|-------------------|
| 點擊和遺漏 | 快取點擊 |
| | 快取遺漏 |
| 取得和設定 | 取得 |
| | 設定 |
| 連線 | 連線的用戶端 |
| 總命令數 | 總作業數 |

如需檢視度量以及自訂本區段中個別圖表的相關資訊，請參閱下列[如何檢視度量以及自訂度量圖表](#how-to-view-metrics-and-customize-charts)一節。

### 使用量圖表

[使用量] 區段有 [Redis 伺服器負載]、[記憶體使用量]、[網路頻寬] 和 [CPU 使用量] 圖表，也會顯示快取執行個的 [定價層]。

![使用量圖表][redis-cache-usage-part]

[定價層] 會顯示快取定價層，而且可以用來將快取[調整](cache-how-to-scale.md)為不同的定價層。

[使用量] 圖表會顯示下列度量。

| 使用量圖表 | 快取度量 |
|-------------------|---------------|
| Redis 伺服器負載 | 伺服器負載 |
| 記憶體使用量 | 已使用的記憶體 |
| 網路頻寬 | 快取寫入 |
| CPU 使用率 | CPU |

如需檢視度量以及自訂本區段中個別圖表的相關資訊，請參閱下列[如何檢視度量以及自訂度量圖表](#how-to-view-metrics-and-customize-charts)一節。
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png

<!---HONumber=AcomDC_0831_2016-->