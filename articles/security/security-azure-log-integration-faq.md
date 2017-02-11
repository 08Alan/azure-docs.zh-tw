---
title: "Azure 記錄整合常見問題集 | Microsoft Docs"
description: "此常見問題集會回答 Azure 記錄整合的相關問題。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: f7589fa62dcfedc6f99439f453a40f999ff8d845
ms.openlocfilehash: aa1e59a38b37c5e78b61ad7fe10f7c8461b7fe1d


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure 記錄整合常見問題集 (FAQ)
此常見問題集會回答有關 Azure 記錄整合的問題，這是種服務，可讓您將來自 Azure 資源的未經處理記錄，整合到內部部署安全性資訊及事件管理 (SIEM) 系統內。 這項整合提供您內部部署或在雲端中所有資產統一的儀表板，以便您彙總、相互關聯、分析和警示與應用程式相關聯的安全性事件。

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>如何查看 Azure 記錄整合從中提取 Azure VM 記錄檔的儲存體帳戶？
執行 **azlog source list**命令。

## <a name="how-can-i-update-the-proxy-configuration"></a>如何更新 Proxy 組態？
如果您的 Proxy 設定不允許直接存取 Azure 儲存體，請開啟 **c:\Program Files\Microsoft Azure Log Integration** 中的 **AZLOG.EXE.CONFIG** 檔案。 更新檔案，以便將組織的 Proxy 位址納入 **defaultProxy** 區段。 更新完成之後，停止並使用 **net stop azlog** 和 **net start azlog** 命令來啟動服務。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>如何查看 Windows 事件中的訂用帳戶資訊？
在新增來源時將 **subscriptionid** 附加到易記名稱的後面。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

事件 XML 具有如下所示的中繼資料，包括訂用帳戶識別碼。

![事件 XML][1]

## <a name="error-messages"></a>錯誤訊息
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>執行 **azlog createazureid**命令時，為什麼收到下列錯誤訊息？
Error:

  *無法建立 AAD 應用程式 - 租用戶 72f988bf-86f1-41af-91ab-2d7cd011db37 - 原因 = 「禁止」 - 訊息 = 「權限不足以完成作業。」*

**Azlog createazureid** 嘗試在 Azure 登入具有存取權的訂用帳戶所有的 Azure AD 租用戶中建立服務主體。 如果您的 Azure 登入在該 Azure AD 租用戶中只是來賓使用者，然後命令會失敗，並出現「權限不足以完成作業」訊息。 要求租用戶系統管理員將您的帳戶新增為租用戶中的使用者。

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>執行 **azlog authorize**命令時，為什麼收到下列錯誤訊息？
Error:

  *建立角色指派警告 - AuthorizationFailed：具有物件識別碼 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' 的用戶端 janedo@microsoft.com' 沒有在 '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000' 範圍內執行動作 'Microsoft.Authorization/roleAssignments/write' 的權限。*

**Azlog authorize** 命令會將 Azure AD 服務主體讀取者的角色指派給 (以 **Azlog createazureid** 建立) 所提供的訂用帳戶。 如果 Azure 登入不是訂用帳戶的共同管理員或擁有者，就會失敗，並出現「授權失敗」錯誤訊息。 需要 Azure 角色型存取控制 (RBAC) 的共同管理員或擁有者才能完成此動作。

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>哪裡可以找到稽核記錄檔中屬性的定義？
請參閱：

* [使用 Resource Manager 來稽核作業](../azure-resource-manager/resource-group-audit.md)
* [在 Azure 監視器 REST API 中列出訂用帳戶中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>哪裡可以找到 Azure 資訊安全中心警示的詳細資訊？
請參閱 [管理及回應 Azure 資訊安全中心的安全性警示](../security-center/security-center-managing-and-responding-alerts.md)。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>如何修改 VM 診斷會收集什麼？
如需如何取得、修改和設定 Windows Azure 診斷 [(WAD)](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 組態的詳細資訊，請參閱 *使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷* 。 以下是範例︰

### <a name="get-the-wad-config"></a>取得 WAD 設定
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>修改 WAD 設定
下列範例是只有從安全性事件記錄檔中收集 EventID 4624 和 EventId 4625 的組態。 會從系統事件記錄檔中收集 Microsoft 反惡意程式碼事件。 如需 XPath 運算式使用方式的詳細資料，請參閱 [取用事件](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>設定 WAD 組態
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

完成變更之後，請檢查儲存體帳戶，以確保會收集正確的事件。

如果您有關於「Azure 記錄整合」的問題，請傳送電子郵件給 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png



<!--HONumber=Feb17_HO2-->


