---
title: Azure 診斷記錄支援的服務和結構描述
description: 了解 Azure 診斷記錄支援的服務和事件結構描述。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 6/08/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 45595893a199b845c8b010bc1e2545b89aa688cd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264974"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 診斷記錄支援的服務、結構描述和類別

[Azure 資源診斷記錄](monitoring-overview-of-diagnostic-logs.md)是您 Azure 資源所發出的記錄，會描述該資源的作業。 這些是資源類型特定的記錄。 在本文中，我們概述一組支援的服務，和每一個服務所發出之事件的事件結構描述。 本文也包含每個資源類型的可用記錄類別完整清單。

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>資源診斷記錄的支援服務和結構描述
資源診斷記錄的結構描述會根據資源和記錄類別而有所不同。   

| 服務 | 結構描述與文件 |
| --- | --- |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理診斷記錄](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 應用程式閘道 |[應用程式閘道的診斷記錄功能](../application-gateway/application-gateway-diagnostics.md) |
| Azure 自動化 |[Azure 自動化的記錄檔分析](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 診斷記錄](../batch/batch-diagnostics.md) |
| 內容傳遞網路 | [CDN 的 Azure 診斷記錄](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 記錄](../cosmos-db/logging.md) |
| Data Factory | [使用 Azure 監視器來監視 Data Factory](../data-factory/monitor-using-azure-monitor.md) |
| 資料湖分析 |[存取 Azure Data Lake Analytics 的診斷記錄](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[存取 Azure Data Lake Store 的診斷記錄](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 適用於 PostgreSQL 的 DB |  無法使用結構描述。 |
| 事件中樞 |[Azure 事件中樞診斷記錄](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | 無法使用結構描述。 |
| IoT 中樞 | [IoT 中樞作業](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 金鑰保存庫記錄](../key-vault/key-vault-logging.md) |
| Load Balancer |[Azure 負載平衡器的 Log Analytics](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 網路安全性群組 |[網路安全性群組 (NSG) 的記錄檔分析](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保護 | [管理 Azure DDoS Protection Standard](../virtual-network/manage-ddos-protection.md) |
| PowerBI 專用 | 無法使用結構描述。 |
| [復原服務] | [Azure 備份的資料模型](../backup/backup-azure-reports-data-model.md)|
| Search |[啟用和使用搜尋流量分析](../search/search-traffic-analytics.md) |
| 服務匯流排 |[Azure 服務匯流排診斷記錄](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 診斷記錄](../sql-database/sql-database-metrics-diag-logging.md) |
| 串流分析 |[作業診斷記錄](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理員 | 無法使用結構描述。 |
| 虛擬網路 | 無法使用結構描述。 |
| 虛擬網路閘道 | 無法使用結構描述。 |

## <a name="supported-log-categories-per-resource-type"></a>每個資源類型支援的記錄檔類別
|資源類型|類別|類別顯示名稱|
|---|---|---|
|Microsoft.AnalysisServices/servers|引擎|引擎|
|Microsoft.AnalysisServices/servers|服務|服務|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 閘道的相關記錄檔|
|Microsoft.Automation/automationAccounts|JobLogs|作業記錄檔|
|Microsoft.Automation/automationAccounts|JobStreams|作業串流|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc 節點狀態|
|Microsoft.Batch/batchAccounts|ServiceLog|服務記錄檔|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|取得端點的計量，例如頻寬、輸出等資訊。|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|管線活動執行記錄|
|Microsoft.DataFactory/factories|PipelineRuns|管線執行記錄|
|Microsoft.DataFactory/factories|TriggerRuns|觸發程序執行記錄|
|Microsoft.DataLakeAnalytics/accounts|稽核|稽核記錄|
|Microsoft.DataLakeAnalytics/accounts|要求|要求記錄檔|
|Microsoft.DataLakeStore/accounts|稽核|稽核記錄|
|Microsoft.DataLakeStore/accounts|Requests|要求記錄檔|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 伺服器記錄|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|PostgreSQL Backup 事件|
|Microsoft.Devices/IotHubs|連線|連線|
|Microsoft.Devices/IotHubs|DeviceTelemetry|裝置遙測|
|Microsoft.Devices/IotHubs|C2DCommands|C2D 命令|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|裝置身分識別作業|
|Microsoft.Devices/IotHubs|FileUploadOperations|檔案上傳作業|
|Microsoft.Devices/IotHubs|路由|路由|
|Microsoft.Devices/IotHubs|D2C 對應項作業|D2C 對應項作業|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D 對應項作業|
|Microsoft.Devices/IotHubs|TwinQueries|對應項查詢|
|Microsoft.Devices/IotHubs|JobsOperations|作業的操作|
|Microsoft.Devices/IotHubs|DirectMethods|直接方法|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E 診斷 (預覽)|
|Microsoft.Devices/provisioningServices|DeviceOperations|裝置作業|
|Microsoft.Devices/provisioningServices|ServiceOperations|服務作業|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|封存記錄檔|
|Microsoft.EventHub/namespaces|OperationalLogs|作業記錄|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動調整規模記錄檔|
|Microsoft.KeyVault/vaults|AuditEvent|稽核記錄|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流程執行階段診斷事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|整合帳戶追蹤事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|網路安全性群組事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|負載平衡器警示事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|負載平衡器探查健全狀況狀態|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保護通知|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保護警示|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|應用程式閘道存取記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|應用程式閘道防火牆記錄檔|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|閘道診斷記錄|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|通道診斷記錄|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|路由診斷記錄|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 診斷記錄|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 診斷記錄|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|流量管理員探查健康情況結果事件|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM 計數器的資料表|
|Microsoft.PowerBIDedicated/capacities|引擎|引擎|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 備份報表資料|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 作業|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 複寫項目|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 複寫統計資料|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 復原點|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 複寫資料上傳速率|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保護的磁碟資料變換|
|Microsoft.Search/searchServices|OperationLogs|作業記錄|
|Microsoft.ServiceBus/namespaces|OperationalLogs|作業記錄|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|查詢存放區執行階段統計資料|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|查詢存放區等候統計資料|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|資料庫等候統計資料|
|Microsoft.Sql/servers/databases|逾時|逾時|
|Microsoft.Sql/servers/databases|區塊|區塊|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|稽核|稽核記錄|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 安全性稽核事件|
|Microsoft.StreamAnalytics/streamingjobs|執行|執行|
|Microsoft.StreamAnalytics/streamingjobs|編寫|編寫|

## <a name="next-steps"></a>後續步驟

* [深入了解診斷記錄](monitoring-overview-of-diagnostic-logs.md)
* [將資源診斷記錄串流至**事件中樞**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure 監視器 REST API 變更資源診斷設定](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 Log Analytics 分析來自 Azure 儲存體的記錄](../log-analytics/log-analytics-azure-storage.md)
