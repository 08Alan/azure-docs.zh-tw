---
title: "將 PowerShell Cmdlet 搭配 Azure RemoteApp 使用 | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中使用 Windows PowerShell Cmdlet。"
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8bea98274f94853cb52fe8b51fdaa4f224c649e4
ms.lasthandoff: 04/27/2017


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>將 Windows PowerShell Cmdlet 搭配 Azure RemoteApp 使用
> [!IMPORTANT]
> Azure RemoteApp 即將於 2017 年 8 月 31 日停止服務。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

 您可以使用 Azure RemoteApp PowerShell Cmdlet 管理和維護您的集合。 首先，請使用下列資訊。

## <a name="get-the-cmdlets"></a>取得 Cmdlet
- - -
先在 [這裡](http://go.microsoft.com/?linkid=9811175)下載 Azure Powershell Cmdlet，當中內含 RemoteApp Cmdlet。 

請查看 [Azure RemoteApp Cmdlet 說明](/powershell/module/azure?view=azuresmps-3.7.0)。

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>設定 Azure Cmdlet 以使用訂用帳戶
- - -
請依照 [本指南](/powershell/azure/overview) 進行，以對 Azure 訂用帳戶使用 Cmdlet。

您可以透過這些步驟快速開始使用：

1. 下載並安裝 [Azure PowerShell Cmdlet](http://go.microsoft.com/?linkid=9811175)。
2. 啟動 Microsoft Azure PowerShell。
3. 執行 **Add-AzureAccount** 以驗證您的 Azure 訂用帳戶。 出現提示時，請輸入您用來登入 Azure 入口網站的使用者名稱和密碼。  
4. 執行 **Get-AzureSubscription** 以列出與您使用者帳戶相關聯的訂用帳戶。 
5. 執行 **Select-AzureSubscription** 並指定要在 PowerShell 主控台中使用的訂用帳戶名稱或識別碼。

恭喜，您的 Azure PowerShell 主控台已設定完成且可以使用了。 請注意，每次您啟動 Azure PowerShell 主控台後都必須重複步驟 2至 5。  

## <a name="create-a-cloud-collection"></a>建立雲端收藏
- - -
十分簡單，只要執行下列命令：

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

上述命令會自動發佈 Microsoft Office 365 應用程式 (Excel、OneNote、Outlook、PowerPoint、Visio 和 Word)。

建立集合可能需要 30 分鐘或更久的時間才能完成。 因此，此命令會傳回可使用的追蹤識別碼，如下所示：

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

完成集合之後，您可以使用下列命令，將使用者新增至集合：

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

大功告成！ 該使用者應該可以使用在 [這裡](https://www.remoteapp.windowsazure.com/)找到的 Azure RemoteApp 用戶端連接到應用程式。

## <a name="available-cmdlets"></a>可用的 Cmdlet
我們還有一些其他命令，將會簡短予以說明：

基本 RemoteApp 集合 Cmdlet： 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

RemoteApp virtual network cmdlets:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

RemoteApp template image cmdlets:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

Other RemoteApp cmdlets:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult


