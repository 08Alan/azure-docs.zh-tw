---
title: Azure 自動化 Runbook 類型
description: 描述您在 Azure 自動化中可使用的各種 Runbook，以及您在決定使用何種類型時應該納入的考量。
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: bwren

---
# Azure 自動化 Runbook 類型
Azure 自動化支援下表簡短描述的四種 Runbook。下列各節提供各種類型的進一步資訊，包括每種類別何時使用的考量。

| 類型 | 說明 |
|:--- |:--- |
| [圖形化](#graphical-runbooks) |以 Windows PowerShell 為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [圖形化 PowerShell 工作流程](#graphical-runbooks) |以 Windows PowerShell 工作流程為基礎，而且完全在 Azure 入口網站的圖形化編輯器中建立和編輯。 |
| [PowerShell](#powershell-runbooks) |以 Windows PowerShell 指令碼為基礎的文字 Runbook。 |
| [PowerShell 工作流程](#powershell-workflow-runbooks) |以 Windows PowerShell 工作流程為基礎的文字 Runbook。 |

## 圖形化 Runbook
[圖形化](automation-runbook-types.md#graphical-runbooks)和圖形化 PowerShell 工作流程 Runbook 是以 Azure 入口網站中的圖形化編輯器來建立和編輯。您可以將它們匯出至檔案，再匯入到另一個自動化帳戶，但無法使用另一種工具來建立或編輯。圖形化 Runbook 會產生 PowerShell 程式碼，但您無法直接檢視或修改此程式碼。圖形化 Runbook 無法轉換成其中一種[文字格式](automation-runbook-types.md)，而文字 Runbook 也無法轉換成圖形化格式。圖形化 Runbook 可以在匯入期間轉換為圖形化 PowerShell 工作流程 Runbook，反之亦然。

### 優點
* 只要具備基本的 [PowerShell](automation-powershell-workflow.md) 知識就能建立 Runbook。
* 以視覺方式呈現管理程序。
* 包含其他 Runbook 做為子 Runbook 來建立高階工作流程。

### 限制
* 無法在 Azure 入口網站之外編輯 Runbook。
* 可能需要包含 PowerShell 程式碼的程式碼活動來執行複雜邏輯。
* 無法檢視或直接編輯圖形化工作流程所建立的 PowerShell 程式碼。請注意，您可以檢視您在任何程式碼活動中所建立的程式碼。

## PowerShell Runbook
PowerShell Runbook 以 Windows PowerShell 為基礎。您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。您也可以使用任何離線文字編輯器，並[匯入 Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) 到 Azure 自動化。

### 優點
* 使用 PowerShell 程式碼實作所有複雜的邏輯，不必處理 PowerShell 工作流程的其他複雜性。
* Runbook 的啟動速度比圖形化或 PowerShell 工作流程 Runbook 更快，因為它在執行之前不需要編譯。

### 限制
* 必須熟悉 PowerShell 指令碼處理。
* 無法使用[平行處理](automation-powershell-workflow.md#parallel-processing)來平行執行多個動作。
* 發生錯誤時無法使用[檢查點](automation-powershell-workflow.md#checkpoints)繼續執行 Runbook。
* 只有使用 Start-AzureAutomationRunbook Cmdlet 才能併入 PowerShell 工作流程 Runbook 和圖形化 Runbook 做為子 Runbook，此 Cmdlet 會建立新的工作。

### 已知問題
以下是 PowerShell Runbook 目前已知的問題。

* PowerShell Runbook 無法擷取具有 Null 值的未加密[變數資產](automation-variables.md)。
* PowerShell Runbook 無法擷取名稱中含有 *~* 的[變數資產](automation-variables.md)。
* PowerShell Runbook 中落入迴圈的 Get-Process 大約在 80 次反覆運算之後就會損毀。
* 如果 PowerShell Runbook 嘗試一次將非常大量的資料寫入輸出資料流，可能會失敗。在處理大型物件時，只輸出您所需的資訊，通常就可以解決這個問題。比方說，不要輸出類似 *Get-Process* 之類的資訊，您可以使用 *Get-Process | Select ProcessName, CPU*，只輸出需要的欄位。

## PowerShell 工作流程 Runbook
PowerShell Workflow Runbook 是以 [Windows PowerShell 工作流程](automation-powershell-workflow.md)為基礎的文字 Runbook。您可以直接使用 Azure 入口網站的文字編輯器來編輯 Runbook 的程式碼。您也可以使用任何離線文字編輯器，並[匯入 Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) 到 Azure 自動化。

### 優點
* 使用 PowerShell 工作流程程式碼實作所有複雜的邏輯。
* 發生錯誤時使用[檢查點](automation-powershell-workflow.md#checkpoints)繼續執行 Runbook。
* 使用[平行處理](automation-powershell-workflow.md#parallel-processing)以平行執行多個動作。
* 可併入其他圖形化 Runbook 和 PowerShell 工作流程 Runbook 成為子 Runbook，以建立高階工作流程。

### 限制
* 作者必須熟悉 PowerShell 工作流程。
* Runbook 必須處理 PowerShell 工作流程額外的複雜性，例如[已還原序列化的物件](automation-powershell-workflow.md#code-changes)。
* Runbook 所需的啟動時間比 PowerShell Runbook 更久，因為它在執行之前需要編譯。
* 只有使用 Start-AzureAutomationRunbook Cmdlet 才能併入 PowerShell Runbook 做為子 Runbook，此 Cmdlet 會建立新的工作。

## 考量
在決定特定 Runbook 要使用何種類型時，您應該考慮下列其他事項。

* 您無法將 Runbook 從圖形化轉換為文字類型，反之亦然。
* 使用不同類型的 Runbook 作為子 Runbook 時有所限制。如需詳細資訊，請參閱 [Azure 自動化中的子 Runbook](automation-child-runbooks.md)。

## 後續步驟
* 若要深入了解如何編寫圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
* 若要了解適用於 Runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱[了解 Windows PowerShell 工作流程](automation-powershell-workflow.md)
* 如需如何建立或匯入 Runbook 的詳細資訊，請參閱[建立或匯入 Runbook](automation-creating-importing-runbook.md)

<!---HONumber=AcomDC_0914_2016-->