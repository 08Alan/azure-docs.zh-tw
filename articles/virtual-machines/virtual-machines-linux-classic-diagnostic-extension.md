
<properties
		pageTitle="用 VM 擴充功能監視 Linux VM | Microsoft Azure"
		description="了解如何使用 Linux 診斷擴充功能監視 Azure 中 Linux VM 的效能和診斷資料。"
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# 使用 Linux 診斷擴充功能監視 Linux VM 的效能和診斷資料

## 簡介

Linux 診斷延伸模組可利用下列功能協助使用者監視在 Microsoft Azure 上執行的 Linux VM：

- 將 Linux VM 的系統效能、診斷和 syslog 資料收集並上傳至使用者的儲存體資料表。
- 讓使用者自訂將要收集並上傳的資料度量。
- 讓使用者將指定的記錄檔上傳至指定的儲存體資料表。

若為 2.0 版，資料包括：

- 所有 Linux Rsyslog 記錄，包括系統、安全性及應用程式記錄檔。
- 在此[文件](https://scx.codeplex.com/wikipage?title=xplatproviders")指定的所有系統資料。
- 使用者指定記錄檔。

請注意，此延伸模組可搭配傳統和資源管理員部署模型使用。


## 如何啟用延伸模組
透過 [Azure 入口網站](https://ms.portal.azure.com/#)、Azure PowerShell 或 Azure CLI 指令碼，可以啟用擴充功能。

若要直接從 Azure 入口網站檢視及設定系統和效能資料，請遵循這些[步驟](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "Windows 部落格的 URL"/)。


本文將著重在透過 Azure CLI 命令啟動及設定延伸模組。這可讓您直接從儲存體資料表讀取和檢視資料。請注意，以下描述的設定方法無法用於 Azure 入口網站。若要直接從 Azure 入口網站檢視和設定系統及效能資料，就必須透過 Azure 入口網站啟用此擴充功能 (如上一段所述)。


## 必要條件
- Microsoft Azure Linux Agent 2.0.6 版或更新版本。請注意，大部分的 Azure VM Linux 資源庫映像包含版本 2.0.6 或更新版本。您可以執行 **WAAgent -version** 以確認 VM 中安裝的版本。如果執行 VM 的版本早於 2.0.6，您可以遵循這些[指示](https://github.com/Azure/WALinuxAgent "指示")來更新它。
- [Azure CLI](../xplat-cli-install.md)。遵循[本指引](../xplat-cli-install.md)在電腦上設定 Azure CLI 環境。安裝好 Azure CLI 之後，您就能從命令列介面 (Bash、終端機、命令提示字元) 中使用 **azure** 命令存取 Azure CLI 命令。例如，執行 **azure vm extension set --help** 取得詳細的使用方式、執行 **azure login** 登入 Azure、執行 **azure vm list** 列出您在 Azure 上的所有虛擬機器。
- 儲存資料的儲存體帳戶。您需要先前建立的儲存體帳戶名稱和存取金鑰才能將資料上傳至您的儲存體。


## 使用 Azure CLI 命令啟動 Linux 診斷延伸模組

###  案例 1.以預設的資料集啟動延伸模組
若為 2.0 版和更新版本，將會收集的預設資料包括：

- 所有 Rsyslog 資訊 (包括系統、安全性及應用程式記錄檔)。  
- 基礎系統資料的核心集，請注意此[文件](https://scx.codeplex.com/wikipage?title=xplatproviders)描述完整的資料集。如果您想要啟用額外的資料，請繼續進行案例 2 和 3 的步驟。

步驟 1.使用下列內容建立名為 PrivateConfig.json 的檔案。

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

步驟 2.執行 **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**。


###   案例 2.自訂效能監視器度量  
本節描述如何自訂效能和診斷資料的資料表。

步驟 1.使用上述「案例 1」的內容建立名為 PrivateConfig.json 的檔案。同時也建立下一個範例中所示名為 PublicConfig.json 的檔案。指定您想要收集的特定資料。

若需所有受支援的提供者和變數，請參考這個[文件](https://scx.codeplex.com/wikipage?title=xplatproviders)。您可以有多個查詢，並藉由將更多查詢附加至指令碼，將它們儲存成多個資料表。

根據預設，一律會收集 Rsyslog 資料。

    {
      	"perfCfg":
      	[
      	    {
      	        "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
      	        "table" : "LinuxMemory"
      	    }
      	]
    }


步驟 2.執行 **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**。


###   案例 3.上傳您自己的記錄檔
本節說明如何收集和上傳特定記錄檔至儲存體帳戶。您必須指定記錄檔的路徑，並指定要儲存記錄檔的資料表名稱。您可以將多個檔案/資料表項目加入至指令碼，以擁有多個記錄檔。

步驟 1.使用「案例 1」的內容建立名為 PrivateConfig.json 的檔案。使用下列內容建立另一個名為 PublicConfig.json 的檔案。

    {
        "fileCfg" : 
        [
            {
                "file" : "/var/log/mysql.err",
                "table" : "mysqlerr"
             }
        ]
    }


步驟 2.執行 **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**。


###   案例 4.停止擴充收集任何記錄檔
本節說明如何停止擴充收集任何記錄檔。請注意，即使使用這項重新設定，監視代理程式處理序仍會啟動並執行。如果您想要完全停止監視代理程式處理序，您可以停用擴充功能來達到此目的。可停用擴充功能的命令是 **azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'**。

步驟 1.使用「案例 1」的內容建立名為 PrivateConfig.json 的檔案。使用下列內容建立另一個名為 PublicConfig.json 的檔案。

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


步驟 2.執行 **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**。


## 檢閱資料
效能和診斷資料會儲存在 Azure 儲存體資料表中。檢閱[這篇文章](../storage/storage-ruby-how-to-use-table-storage.md)以了解如何使用 Azure CLI 指令碼存取儲存體資料表中的資料。

此外，您可以使用下列 UI 工具來存取資料：

1.	使用 Visual Studio 伺服器總管。瀏覽至儲存體帳戶。執行 VM 約 5 分鐘後，您應該會看到四個預設資料表："LinuxCpu"、"LinuxDisk"、"LinuxMemory" 和 "Linuxsyslog"。按兩下資料表名稱以檢視資料。
2.	使用 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/ "Azure 儲存體總管")存取資料。

![image](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

如果已啟用案例 2 和 3 中指定的 fileCfg 或 perfCfg，您可以使用先前的工具來檢視非預設的資料。



## 已知問題
- 若為 2.0 版，只能透過指令碼存取 Rsyslog 資訊和客戶指定記錄檔。

<!---HONumber=AcomDC_0601_2016-->