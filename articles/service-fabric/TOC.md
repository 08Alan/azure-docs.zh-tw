
# 概觀
## [什麼是 Service Fabric？](service-fabric-overview.md)
## [了解微服務](service-fabric-overview-microservices.md)
## [概觀](service-fabric-content-roadmap.md)
## [應用程式案例](service-fabric-application-scenarios.md)
## [模式和案例](service-fabric-patterns-and-scenarios.md)
## [架構](service-fabric-architecture.md)
## [術語](service-fabric-technical-overview.md)

# 開始使用
## 設定開發環境
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## 建立您的第一個應用程式
### [Windows 上的 C# ](service-fabric-create-your-first-application-in-visual-studio.md)
### [在 Linux 上使用 Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux 上的 C](service-fabric-create-your-first-linux-application-with-csharp.md)
## [在本機叢集上部署應用程式](service-fabric-get-started-with-a-local-cluster.md)
## [在容器中部署 .NET 應用程式](service-fabric-host-app-in-a-container.md)
## [在 Azure 上建立您的第一個叢集](service-fabric-get-started-azure-cluster.md)
## [建立您的第一個獨立叢集](service-fabric-get-started-standalone-cluster.md)
## [建立您的第一個容器應用程式](service-fabric-get-started-containers.md)

# 作法
## 建置應用程式
  
### 概念
#### [支援的程式設計模型](service-fabric-choose-framework.md)
#### [應用程式模型](service-fabric-application-model.md)
#### [主機模型](service-fabric-hosting-model.md)
#### [服務資訊清單資源](service-fabric-service-manifest-resources.md)
#### [服務狀態](service-fabric-concepts-state.md)
#### [服務分割](service-fabric-concepts-partitioning.md)
#### [服務可用性](service-fabric-availability-services.md)
#### [應用程式的延展性](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [規劃應用程式的容量](service-fabric-capacity-planning.md)

### 建置可供來賓執行的服務
#### [部署來賓可執行檔](service-fabric-deploy-existing-app.md)
#### [部署多個來賓可執行檔](service-fabric-deploy-multiple-apps.md)

### 建置容器服務
#### [概觀](service-fabric-containers-overview.md)
#### [部署 Windows 容器](service-fabric-deploy-container.md)
#### [部署 Linux 容器](service-fabric-deploy-container-linux.md)
#### [Docker Compose (預覽)](service-fabric-docker-compose.md)
#### [容器和服務的資源管理](service-fabric-resource-governance.md)
#### [磁碟區與記錄驅動程式](service-fabric-containers-volume-logging-drivers.md)

### 建置 Reliable Service 服務
#### [概觀](service-fabric-reliable-services-introduction.md)
#### 概念
##### [Reliable Services 生命週期 - C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services 生命週期 - Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
##### [Reliable Collections 指導方針與建議](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [交易和鎖定](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [可靠的並行佇列](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [可靠的集合序列化](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager 與 Reliable Collection 內部](service-fabric-reliable-services-reliable-collections-internals.md)

#### 開始使用
##### [Windows 上的 C# ](service-fabric-reliable-services-quick-start.md)
##### [在 Linux 上使用 Java](service-fabric-reliable-services-quick-start-java.md)

#### [設定](service-fabric-reliable-services-configuration.md)
#### [傳送通知](service-fabric-reliable-services-notifications.md)
#### [備份與還原](service-fabric-reliable-services-backup-restore.md)

#### 與服務通訊
##### [與 Reliable Services 通訊](service-fabric-reliable-services-communication.md)
##### [服務遠端處理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [服務遠端處理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [安全通訊 - C#](service-fabric-reliable-services-secure-communication.md)
##### [安全通訊 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [進階用量](service-fabric-reliable-services-advanced-usage.md)

### 建置 Reliable Actor 服務
#### [概觀](service-fabric-reliable-actors-introduction.md)
#### 概念
##### [架構](service-fabric-reliable-actors-platform.md)
##### [生命週期與記憶體回收](service-fabric-reliable-actors-lifecycle.md)
##### [狀態管理](service-fabric-reliable-actors-state-management.md)
##### [Polymorphism](service-fabric-reliable-actors-polymorphism.md)
##### [重新進入](service-fabric-reliable-actors-reentrancy.md)
##### [類型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### 開始使用
##### [Windows 上的 C# ](service-fabric-reliable-actors-get-started.md)
##### [在 Linux 上使用 Java](service-fabric-reliable-actors-get-started-java.md)

#### [傳送通知](service-fabric-reliable-actors-events.md) 
#### [設定計時器和提醒](service-fabric-reliable-actors-timers-reminders.md)
#### [設定 KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [設定通訊設定](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [設定 ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### 與服務通訊
#### [服務通訊](service-fabric-connect-and-communicate-with-services.md)
#### [DNS 服務](service-fabric-dnsservice.md)
#### [反向 Proxy](service-fabric-reverseproxy.md)
#### [設定安全通訊的反向 proxy](service-fabric-reverseproxy-configure-secure-communication.md)

### [新增 Web 前端](service-fabric-add-a-web-frontend.md)

### 在整合式開發環境 (IDE) 中工作
#### [開始使用 Eclipse 外掛程式進行 Java 部署](service-fabric-get-started-eclipse.md)
#### [管理 Visual Studio 的應用程式](service-fabric-manage-application-in-visual-studio.md)
#### [在 Visual Studio 中設定安全連線](service-fabric-visualstudio-configure-secure-connections.md)
#### [針對多種環境設定您的應用程式](service-fabric-manage-multiple-environment-app-configuration.md)

### 設定安全性
#### [管理應用程式密碼](service-fabric-application-secret-management.md)  
#### [設定應用程式的安全性原則](service-fabric-application-runas-security.md)

### 偵錯
#### [在 VS 的 C# 服務中進行偵錯](service-fabric-debugging-your-application.md)
#### [在 Eclipse 中的Java 服務中進行偵錯](service-fabric-debugging-your-application-java.md)
#### [常見錯誤及例外狀況](service-fabric-errors-and-exceptions.md)

### 在本機上監視及診斷
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### 與 API 管理整合
#### [概觀](service-fabric-api-management-overview.md)
#### [快速入門](service-fabric-api-management-quick-start.md)

### 從雲端服務移轉
#### [比較雲端服務與 Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [移轉室 Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [建議的實務作法](/azure/architecture/service-fabric/migrate-from-cloud-services)

## 管理應用程式生命週期
### [概觀](service-fabric-application-lifecycle.md)
### [封裝應用程式](service-fabric-package-apps.md)
### [了解 ImageStoreConnectionString 設定](service-fabric-image-store-connection-string.md)
### 部署或移除應用程式
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
### 升級應用程式
#### [概觀](service-fabric-application-upgrade.md)
#### [設定應用程式升級](service-fabric-visualstudio-configure-upgrade.md)
#### [應用程式升級參數](service-fabric-application-upgrade-parameters.md)
#### 升級
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)
#### [應用程式升級的資料序列化](service-fabric-application-upgrade-data-serialization.md)
#### [應用程式升級進階主題](service-fabric-application-upgrade-advanced.md)

### 測試應用程式與服務
#### [錯誤分析概觀](service-fabric-testability-overview.md)
#### [測試服務對服務的通訊](service-fabric-testability-scenarios-service-communication.md)
#### 模擬失敗
##### [使用受控的混亂](service-fabric-controlled-chaos.md)
##### [使用測試動作](service-fabric-testability-actions.md)
##### [工作負載期間](service-fabric-testability-workload-tests.md)
##### [使用測試案例](service-fabric-testability-scenarios.md)
##### [使用節點轉換 API](service-fabric-node-transition-apis.md)
#### [進行應用程式負載測試](service-fabric-vso-load-test.md)

### 設定連續整合
#### [透過 VSTS 設定連續整合](service-fabric-set-up-continuous-integration.md)
#### [使用 Jenkins 部署 Linux Java 應用程式](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## 建立和管理叢集

### [概觀](service-fabric-deploy-anywhere.md)
### 概念
#### [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [叢集安全性](service-fabric-cluster-security.md)
#### [Linux 和 Windows 間的功能差異](service-fabric-linux-windows-differences.md)

### 規劃和準備
#### [容量規劃](service-fabric-cluster-capacity.md)
#### [災害復原](service-fabric-disaster-recovery.md)

### Azure 上的叢集
#### 概念
##### [節點類型與 VM 擴展集](service-fabric-cluster-nodetypes.md)
##### [叢集網路模式](service-fabric-patterns-networking.md)
#### 建立 
##### [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Stuido 與 Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### 調整 
##### [以手動方式](service-fabric-cluster-scale-up-down.md)
##### [以程式設計方式](service-fabric-cluster-programmatic-scaling.md)
#### [升級](service-fabric-cluster-upgrade.md)
#### [設定存取控制](service-fabric-cluster-security-roles.md)
#### [設定](service-fabric-cluster-fabric-settings.md)
#### [管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md) 
#### [刪除](service-fabric-cluster-delete.md)

### 獨立叢集
#### [規劃並準備部署](service-fabric-cluster-standalone-deployment-preparation.md)
#### 建立
##### [建立內部部署](service-fabric-cluster-creation-for-windows-server.md)
##### [在 Azure 虛擬機器上建立](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [使用憑證保護安全](service-fabric-windows-cluster-x509-security.md)  
##### [使用 Windows 安全性保護安全](service-fabric-windows-cluster-windows-security.md)
##### [獨立套件的內容](service-fabric-cluster-standalone-package-contents.md)
#### [調整](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [設定存取控制](service-fabric-cluster-security-roles.md)
#### [設定](service-fabric-cluster-manifest.md)
#### [升級](service-fabric-cluster-upgrade-windows-server.md) 

### [視覺化叢集](service-fabric-visualizing-your-cluster.md)
### [連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)

### [使用 XPlat CLI 管理叢集](service-fabric-azure-cli.md)
### [使用 Azure CLI 2.0 命令管理叢集](service-fabric-azure-cli-2-0.md)
### [修補叢集節點](service-fabric-patch-orchestration-application.md)

### 管理和組織叢集資源
#### [叢集資源管理員概觀](service-fabric-cluster-resource-manager-introduction.md)
#### [叢集資源管理員架構](service-fabric-cluster-resource-manager-architecture.md)
#### [描述叢集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [應用程式群組概觀](service-fabric-cluster-resource-manager-application-groups.md)
#### [設定叢集資源管理員的設定](service-fabric-cluster-resource-manager-configure-services.md)
#### [資源耗用計量](service-fabric-cluster-resource-manager-metrics.md)
#### [使用服務親和性](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [服務放置原則](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [管理叢集](service-fabric-cluster-resource-manager-management-integration.md)
#### [叢集磁碟重組](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [平衡叢集](service-fabric-cluster-resource-manager-balancing.md)
#### [節流](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [服務動作](service-fabric-cluster-resource-manager-movement-cost.md)

## 監視與診斷
### [監視與診斷應用程式](service-fabric-diagnostics-overview.md)
### 產生事件
#### [產生基礎結構層級事件](service-fabric-diagnostics-event-generation-infra.md)
##### [Reliable Services 事件](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors 事件](service-fabric-reliable-actors-diagnostics.md)
#### [產生應用程式層級事件](service-fabric-diagnostics-event-generation-app.md)
### 檢查應用程式與叢集健全狀況
#### [監視 Service Fabric 健全狀況](service-fabric-health-introduction.md)
#### [回報和檢查服務健全狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [新增自訂健全狀況報表](service-fabric-report-health.md)
#### [使用系統健全狀況報表進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [檢視健全狀況報表](service-fabric-view-entities-aggregated-health.md)
### 彙總事件
#### [使用 EventFlow 彙總事件](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### 使用 Azure 診斷彙總事件
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### 分析事件
#### [使用 Application Insights 分析事件](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [使用 OMS 分析事件](service-fabric-diagnostics-event-analysis-oms.md)
### [針對本機叢集進行疑難排解](service-fabric-troubleshoot-local-cluster-setup.md)

# 參考
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI](/cli/azure/sf)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/)
## [Service Fabric 的常見問題](service-fabric-common-questions.md)
## [學習路徑](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [價格](https://azure.microsoft.com/pricing/details/service-fabric/)
## [範例程式碼](http://aka.ms/servicefabricsamples)
## [Service Fabric 支援選項](service-fabric-support.md)
## [服務更新](https://azure.microsoft.com/updates/?product=service-fabric)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
