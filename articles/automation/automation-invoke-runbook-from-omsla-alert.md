---
title: "從 Log Analytics Alert 呼叫 Azure 自動化 Runbook | Microsoft Docs"
description: "這篇文章提供如何從 Microsoft OMS Log Analytics Alert 呼叫自動化 Runbook 的概觀。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8460ed6be3e922fb85f46982662d44eed21dda7c
ms.lasthandoff: 03/18/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>從 OMS Log Analytics Alert 呼叫 Azure 自動化 Runbook | Microsoft Docs

在 Log Analytics 中設定警示，以便在結果符合某個特定準則時建立警示記錄 (例如處理器使用率長時間處於峰值，或對於商務應用程式功能的重要特定應用程式處理序失敗)，並將相對應的事件寫入 Windows 事件記錄檔中，該警示會自動執行自動化 Runbook，以嘗試自動修復該問題。  

設定警示時有兩種選項可呼叫 Runbook。  具體而言，

1. 使用 Webhook。
   * 如果您的 OMS 工作區未連結到自動化帳戶，這是唯一可用的選項。
   * 如果您已有連結至 OMS 工作區的自動化帳戶，還是可以使用此選項。  

2. 直接選取 Runbook。
   * OMS 工作區連結至自動化帳戶時，才使用此選項。  

## <a name="calling-a-runbook-using-a-webhook"></a>使用 Webhook 來呼叫 Runbook

Webhook 可讓您在 Azure 自動化中，透過單一 HTTP 要求啟動特定的 Runbook。  在設定 [Log Analytics Alert](../log-analytics/log-analytics-alerts.md#creating-alert-rules) 以使用 Webhook 呼叫 Runbook 作為警示動作前，您必須先為將使用此方法呼叫的 Runbook 建立 Webhook。  檢閱並遵循[建立 Webhook](automation-webhooks.md#creating-a-webhook) 文章且記得記錄 Webhook URL，如此您就可以在設定警示規則時加以參考。   

## <a name="calling-a-runbook-directly"></a>直接呼叫 Runbook

如果您已在 OMS 工作區中安裝並設定自動化與控制項供應項目，在為警示設定 Runbook 動作選項時，您可以從**選取 Runbook** 下拉式清單檢視所有 Runbook，並選取您想要回應警示而執行的特定 Runbook。  選取的 Runbook 可以在 Azure 雲端或混合式 Runbook 背景工作角色上的工作區中執行。  使用 [Runbook] 選項來建立警示時，會為 Runbook 建立一個 Webhook。  如果您移至自動化帳戶，並瀏覽至選取的 Runbook 的 Webhook 刀鋒視窗中，就會看到 Webhook。  如果您刪除警示，將不會刪除 Webhook，但使用者可以手動刪除 Webhook。  如果 Webhook 未遭刪除也沒關係，它只是個被遺棄的項目，最終必須刪除，才能維護組織的自動化帳戶。  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Runbook 的特性 (適用於兩個選項)

從 Log Analytics 警示呼叫 Runbook 這兩種方法具有不同的行為特性，您必須先了解其行為特性再設定警示規則。  

* 您必須擁有名為 **WebhookData** (也就是**物件**類型) 的 Runbook 輸入參數。  它可以是強制性或選擇性。  警示會使用此輸入參數將搜尋結果傳遞給 Runbook。

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  您必須有將 WebhookData 轉換成 PowerShell 物件的程式碼。

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* 將會是物件的陣列；每個物件都包含某個搜尋結果的值欄位

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Webhook 選項和 Runbook 選項之間的 WebhookData 不一致

* 設定要呼叫 Webhook 的警示，請輸入您為 Runbook 建立的 Webhook URL，然後按一下 [測試 Webhook] 按鈕。  傳送至 Runbook 產生的 WebhookData 不包含任何 *.SearchResul* 或 *.SearchResults*。

*  如果您儲存該警示，警示觸發並呼叫 Webhook 時，傳送到 Runbook 的 WebhookData 包含 *.SearchResult*。
* 如果您建立警示並將其設定為呼叫 Runbook (這也會建立 webhook)，警示觸發時，會將 WebhookData 傳送至內含 *.SearchResults* 的 Runbook。

因此在上述程式碼範例中，如果警示呼叫的是 webhook，您必須取得 *.SearchResult*，而如果警示直接呼叫 Runbook，則必須取得 *.SearchResults*。

## <a name="example-walkthrough"></a>範例逐步解說

我們將透過下列範例圖形化 Runbook (其會啟動 Windows 服務) 示範其運作方式。<br><br> ![啟動 Windows 服務圖形化 Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Runbook 有一個類型為**物件**的輸入參數，其名為 **WebhookData**，其中所含的 webhook 資料是從包含 *.SearchResults* 的警示所傳遞。<br><br> ![Runbook 輸入參數](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

例如，在 Log Analytics 中，我們建立兩個自訂欄位 *SvcDisplayName_CF* 和*SvcState_CF*，以從系統事件記錄檔寫入的事件中，擷取服務顯示名稱和服務的狀態 (也就是執行中或已停止)。  接著我們會使用下列搜尋查詢建立警示規則：`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`，如此我們就可以偵測列印多工緩衝處理器服務在 Windows 系統上停止的時間。  它可以是任何您想使用的服務，但此範例中，我們會參考其中一個隨附於 Windows 作業系統中預先存在的服務。  警示動作會設定為執行此範例中使用的 Runbook，並在混合式 Runbook 背景工作角色上執行，而會在目標系統上啟用此兩種設定。   

Runbook 程式碼活動**從 LA 取得服務名稱**，會將 JSON 格式的字串轉換成項目 SvcDisplayName_CF 上的物件類型和篩選器，以擷取 Windows 服務的顯示名稱，並將此值傳遞到下一個活動，該活動會先確認服務已停止，然後再嘗試重新啟動它。  SvcDisplayName_CF 是在 Log Analytics 中建立的[自訂欄位](../log-analytics/log-analytics-custom-fields.md)，用來示範此範例。

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

服務停止時，Log Analytics 中的警示規則將會偵測相符項目、觸發 Runbook 並將警示內容傳送至 Runbook。 Runbook 將會採取動作來確認服務已停止，若是則嘗試重新啟動服務，並確認它是否正確啟動，並輸出結果。     

或者如果您沒有連結至 OMS 工作區的自動化帳戶，您可以設定具 webhook 動作的警示規則，來觸發 runbook 並將 runbook 設定為轉換 JSON 格式的字串，並對遵循先前所述指引的 *.SearchResult* 進行篩選。    

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Log Analytics 的警示以及如何建立，請參閱 [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md)。

* 若要了解如何使用 Webhook 來觸發 Runbook，請參閱 [Azure 自動化 Webhook](automation-webhooks.md)。

