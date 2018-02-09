---
title: "使用 Azure Log Analytics 從您的環境收集資料 | Microsoft Docs"
description: "此主題可協助您了解如何使用 Log Analytics 來收集資料並監視您內部部署環境或其他雲端環境中裝載的電腦。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: magoedte
ms.openlocfilehash: d12743b752c42e6a7373e9c15df6dac71b7f9d27
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>使用 Log Analytics 從您的環境中的電腦收集資料

Azure Log Analytics 可以從位於下列位置的 Windows 或 Linux 電腦收集並處理資料：

* [Azure 虛擬機器](log-analytics-quick-collect-azurevm.md) (使用 Log Analytics VM 延伸模組) 
* 您的資料中心作為實體伺服器或虛擬機器
* 雲端代管服務 (例如 Amazon Web 服務 (AWS)) 中的虛擬機器

在您的環境中裝載的電腦可以直接連線到 Log Analytics，或如果您已經使用 System Center Operations Manager 2012 R2 或 2016 監視這些電腦，您可以將您的 Operations Manage 管理群組與 Log Analytics 整合並繼續維持您的服務作業程式與策略。  

## <a name="overview"></a>概觀

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

分析並處理收集的資料之前，您必須先在每一部您想要傳送資料到 Log Analytics 服務的電腦上安裝代理程式並連線。 您可以使用安裝程式、命令列或 Azure 自動化中的預期狀態設定 (DSC) 在您的內部部署電腦上安裝代理程式。 

適用於 Linux 與 Windows 的代理程式會透過 TCP 連接埠 443 對外與 Log Analytics 服務通訊，且如果電腦連線至防火牆或 Proxy 伺服器以透過網際網路通訊，請檢閱[設定代理程式以搭配 Proxy 伺服器或 OMS 閘道使用](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway)，以了解必須套用哪些設定變更。 如果您已使用 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 來監視電腦，則該電腦可以具有多重主目錄，並使用 Log Analytics 服務來收集資料並轉送到該服務，且仍然受到 [Operations Manager](log-analytics-om-agents.md) 監視。 由與 Log Analytics 整合之 Operations Manager 管理群組監視的 Linux 電腦不會收到資料來源設定並透過管理群組轉送收集的資料。 Windows 代理程式可已回報到最多四個工作區，而 Linux 代理程式只支援回報到單一工作區。  

適用於 Linux 與 Windows 的代理程式不僅可用於連線到 Log Analytics，它也支援與「Azure 自動化」連線以裝載「混合式 Runbook」背景工作角色與管理解決方案，例如「變更追蹤與更新管理」。  如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../automation/automation-offering-get-started.md#automation-architecture-overview)。  

如果 IT 安全性原則不允許您網路上的電腦連線到網際網路，則可以將代理程式設定為連線到 OMS 閘道，以根據您已啟用的解決方案來接收組態資訊和傳送收集到的資料。 如需如何設定 Linux 或 Windows 代理程式以透過 OMS 閘道與 Log Analytics 服務進行通訊的詳細資訊和步驟，請參閱[使用 OMS 閘道將電腦連線到 OMS](log-analytics-oms-gateway.md)。 

> [!NOTE]
> 適用於 Windows 的代理程式只支援傳輸層安全性 (TLS) 1.0 與 1.1。  
> 

## <a name="prerequisites"></a>先決條件
開始之前請檢閱下列詳細資料，以確認符合最低系統需求。

### <a name="windows-operating-system"></a>Windows 作業系統
Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2008 Service Pack 1 (SP1) 或更新版本
* Windows 7 SP1 與更新版本

#### <a name="network-configuration"></a>網路設定
下列資訊列出 Windows 代理程式與 Log Analytics 通訊所需的 Proxy 和防火牆設定資訊。 流量會從您的網路輸出至 Log Analytics 服務。 

| 代理程式資源 | 連接埠 | 略過 HTTPS 檢查|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | yes |
|*.oms.opinsights.azure.com | 443 | yes | 
|*.blob.core.windows.net | 443 | yes | 
|*.azure-automation.net | 443 | yes | 

### <a name="linux-operating-systems"></a>Linux 作業系統
以下為正式支援的 Linux 散發套件。  不過，Linux 代理程式也可能在未列出的其他散發套件上執行。

* Amazon Linux 2012.09 至 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

#### <a name="network-configuration"></a>網路組態
下列資訊列出 Linux 代理程式與 Log Analytics 通訊所需的 Proxy 和防火牆設定資訊。 流量會從您的網路輸出至 Log Analytics 服務。 

|代理程式資源| 連接埠 |  
|------|---------|  
|*.ods.opinsights.azure.com | 連接埠 443|   
|*.oms.opinsights.azure.com | 連接埠 443|   
|*.blob.core.windows.net | 連接埠 443|   
|*.azure-automation.net | 連接埠 443|  

Linux 代理程式支援使用 HTTPS 通訊協定，透過 Proxy 伺服器或 OMS 閘道，與 Log Analytics 服務進行通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  可在安裝期間指定或在安裝後透過修改 proxy.conf 組態檔案指定的 Proxy 伺服器。  

Proxy 組態值的語法如下︰

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 若您的 Proxy 伺服器不要求您進行驗證，Linux 代理程式仍會要求提供虛擬使用者/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 描述 |
|--------|-------------|
|通訊協定 | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|password | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/OMS 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/OMS 閘道的選擇性連接埠號碼 |

例如：`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 若您在您的密碼中使用特殊字元 (例如 “@”)，您會收到 Proxy 連線錯誤，因為系統無法正確剖析該值。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  

## <a name="install-and-configure-agent"></a>安裝及設定代理程式 
視您的需求使用不同的方法，即可將您的內部部署電腦直接與 Log Analytics 連線。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

|來源 | 方法 | 描述|
|-------|-------------|-------------|
| Windows 電腦|- [手動安裝](log-analytics-agent-windows.md)<br>- [Azure 動化 DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [搭配 Azure Stack 的資源管理員範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |從命令列或使用自動化方法 (例如 Azure 自動化 DSC、[System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)) 來安裝 Microsoft Monitoring 代理程式，或者如果您已經在您的資料中心部署 Microsoft Azure Stack，請使用 Azure Resource Manager 範本。| 
|Linux 電腦| [手動安裝](log-analytics-quick-collect-linux-computer.md)|安裝適用於 Linux 的代理程式需要 GitHub 上裝載的包裝函式指令碼。 | 
| System Center Operations Manager|[將 Operations Manager 與 Log Analytics 整合](log-analytics-om-agents.md) | 設定 Operations Manager 與 Log Analytics 之間的整合，以將收集的資料從 Linux 與 Windows 電腦報告轉送到管理群組。|  

## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](log-analytics-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 

* 了解[記錄搜尋](log-analytics-log-searches.md)，它可以分析從資料來源和方案所收集的資料。 

* 了解可將功能加入 Log Analytics 並會將資料收集到 OMS 存放庫的[方案](log-analytics-add-solutions.md) 。