---
title: 收集並分析 Azure 活動記錄在 Log Analytics 工作區 |Microsoft Docs
description: 您可以使用 Azure 活動記錄解決方案來分析和搜尋所有 Azure 訂用帳戶的 Azure 活動記錄。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: magoedte
ms.openlocfilehash: 48fb09b73a6169da392443f5fbf4f005e9640c3e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905982"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>收集並分析 Azure 活動記錄中 Azure 監視器中的 Log Analytics 工作區

![Azure 活動記錄符號](./media/collect-activity-logs/activity-log-analytics.png)

活動記錄分析解決方案可協助您分析和搜尋所有 Azure 訂用帳戶的 [Azure 活動記錄](../../azure-monitor/platform/activity-logs-overview.md)。 Azure 活動記錄能為您提供訂用帳戶中的資源所執行的作業有關的深入解析。 活動記錄檔之前稱為「稽核記錄」或「作業記錄」，因為它會報告訂用帳戶的事件。

您可以使用活動記錄檔來判斷訂用帳戶中的資源上任何寫入作業 (PUT、POST、DELETE) 的「*內容*」、「對象」和「時間」。 您也可以了解作業的狀態和其他相關屬性。 活動記錄不包含讀取 (GET) 作業，或使用傳統部署模型的資源有關的作業。

當您將 Azure 活動記錄連線到 Log Analytics 工作區時，您可以：

- 使用預先定義檢視分析活動記錄
- 分析和搜尋多個 Azure 訂用帳戶的活動記錄
- 保留活動記錄超過 90 天<sup>1</sup>
- 使活動記錄與其他 Azure 平台和應用程式資料產生關聯
- 請參閱依狀態彙總的作業活動
- 檢視每個 Azure 服務發生的活動所呈現的趨勢
- 報告所有 Azure 資源的授權變更
- 找出影響資源的中斷或服務健全狀況問題
- 使用記錄搜尋使使用者活動、 自動調整作業、授權變更和服務健全狀況與您環境的其他記錄或度量資訊產生關聯

<sup>1</sup>根據預設，Azure 監視器會保留您的 Azure 活動記錄檔 Log Analytics 工作區中 90 天，即使您在免費層。 或者，如果您有少於 90 天的工作區中保留期設定。 如果工作區的保留期超過 90 天，即會根據工作區的保留期間來保留活動記錄。

Log Analytics 工作區會收集活動記錄免費的並將記錄檔儲存 90 天免費。 如果儲存記錄超過 90 天，對於儲存超過 90 天的資料將產生資料保留期費用。

您在免費定價層時，活動記錄不適用於每日資料耗用量。

## <a name="connected-sources"></a>連接的來源

不同於大部分其他的 Azure 監視解決方案中，資料不會收集活動記錄檔的代理程式。 解決方案使用的所有資料直接來自於 Azure。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [Windows 代理程式](../../azure-monitor/platform/agent-windows.md) | 否 | 解決方案不會收集來自 Windows 代理程式的資訊。 |
| [Linux 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 解決方案不會收集來自 Linux 代理程式的資訊。 |
| [SCOM 管理群組](../../azure-monitor/platform/om-agents.md) | 否 | 解決方案不會收集來自連線 SCOM 管理群組的代理程式之中的資訊。 |
| [Azure 儲存體帳戶](collect-azure-metrics-logs.md) | 否 | 解決方案不會收集來自 Azure 儲存體的資訊。 |

## <a name="prerequisites"></a>必要條件

- 若要存取 Azure 活動記錄資訊，您必須有 Azure 訂用帳戶。

## <a name="configuration"></a>組態

執行下列步驟來設定您工作區的 Activity Log Analytics 解決方案。

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](../../azure-monitor/insights/solutions.md)中所述的程序，啟用 Activity Log Analytics 解決方案。
2. 設定活動記錄移至您的 Log Analytics 工作區。
    1. 在 Azure 網站中，選取您的工作區，然後按一下 [Azure 活動記錄]。
    2. 對於每個訂用帳戶，按一下訂用帳戶名稱。  
        ![新增訂用帳戶](./media/collect-activity-logs/add-subscription.png)
    3. 在 [SubscriptionName] 刀鋒視窗中，按一下 [連接]。  
        ![連線訂用帳戶](./media/collect-activity-logs/subscription-connect.png)

登入 Azure 入口網站，將 Azure 訂用帳戶連接至您的工作區。  

## <a name="using-the-solution"></a>使用解決方案

您將 Activity Log Analytics 解決方案新增至您的工作區時，**Azure 活動記錄**圖格會新增至概觀儀表板。 此圖格會顯示解決方案存取的訂用帳戶有關的 Azure 活動記錄數目計數。

![Azure 活動記錄圖格](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>檢視 Azure 活動記錄

按一下 [Azure 活動記錄] 圖格以開啟 [Azure 活動記錄] 儀表板。 此儀表板包含下表中的刀鋒視窗。 每個刀鋒視窗最多會列出 10 個與該刀鋒視窗中指定範圍和時間範圍的準則相符的項目。 您可以按一下刀鋒視窗底部的 [查看全部]，或按一下刀鋒視窗標頭，以執行記錄搜尋來傳回所有記錄。

您已設定您的活動記錄移至解決方案*之後*，活動記錄資料才會出現，因此您在此之前無法檢視資料。

| 刀鋒視窗 | 描述 |
| --- | --- |
| Azure 活動記錄項目 | 對於您選取的日期範圍，顯示最上方 Azure 活動記錄項目記錄總計的長條圖，並顯示前 10 個活動呼叫端的清單。 按一下長條圖即可執行 <code>AzureActivity</code> 的記錄搜尋。 按一下呼叫端項目執行記錄搜尋，傳回該項目的所有活動記錄項目。 |
| 依狀態列出的活動記錄 | 對於您選取的日期範圍，顯示 Azure 活動記錄狀態的環圈圖。 另外也顯示前 10 筆狀態記錄的清單。 按一下圖表即可執行 <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code> 的記錄搜尋。 按一下狀態項目執行記錄搜尋，傳回該狀態記錄的所有活動記錄項目。 |
| 依資源列出的活動記錄 | 顯示活動記錄的資源總數，並對於各個資源列出記錄計數的前 10 個資源。 按一下總計區域即可執行 <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code> 的記錄搜尋，這會顯示解決方案可用的所有 Azure 資源。 按一下資源來執行記錄搜尋，以傳回該資源的所有活動記錄。 |
| 依資源提供者列出的活動記錄 | 顯示產生活動記錄的資源提供者總數，並列出前 10 個。 按一下總計區域即可執行 <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code> 的記錄搜尋，這會顯示所有 Azure 資源提供者。 按一下資源提供者執行記錄搜尋，以傳回提供者的所有活動記錄。 |

![Azure 活動記錄儀表板](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>後續步驟

- 特定活動時建立[警示](../../azure-monitor/platform/alerts-metric.md)。
- 使用[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)檢視活動記錄的詳細資訊。
