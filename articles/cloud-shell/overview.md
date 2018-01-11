---
title: "Azure Cloud Shell 概觀 | Microsoft Docs"
description: "Azure Cloud Shell 的概觀。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: juluk
ms.openlocfilehash: 129b43db85a0962005352e0f1e6ad2ad3be2c7d5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概觀
Azure Cloud Shell 是可經由瀏覽器存取的互動式殼層，應用在 Azure 資源管理上。
它可讓您彈性地選擇最適合您工作方式的殼層體驗。
Linux 使用者可以選擇 Bash 體驗，而 Windows 使用者可以選擇 PowerShell。

請嘗試從 shell.azure.com 使用此按鈕。

[![](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure 雲端命令介面")](https://shell.azure.com)

請嘗試從 Azure 入口網站使用的雲端殼層圖示。

![入口網站啟動](media/overview/portal-launch-icon.png)

## <a name="features"></a>特性
### <a name="browser-based-shell-experience"></a>以瀏覽器為基礎的體驗
Cloud Shell 以 Azure 管理工作為考量，提供以瀏覽器存取命令列的體驗。
使用 Cloud Shell 可以只有雲端才能提供的方式離開本機電腦工作。

### <a name="choice-of-preferred-shell-experience"></a>選擇慣用的殼層體驗
Linux 使用者時可以使用 Bash 雲端殼層中的 Windows 使用者可以在雲端介面 （預覽） 中使用 PowerShell 從殼層下拉式清單。

![Cloud Shell 中的 Bash](media/overview/overview-bash-pic.png)

![Cloud Shell 中的 PowerShell (預覽)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>驗證及設定 Azure 工作站
由 Microsoft 管理雲端 Shell，讓它隨附常用的命令列工具和語言支援。 雲端殼層也安全地讓您立即存取您的資源，透過 Azure CLI 2.0 或 Azure PowerShell cmdlet 自動驗證。

檢視 [Bash 經驗](features.md#tools)和 [PowerShell (預覽) 經驗](features-powershell.md#tools)的完整工具清單。

### <a name="multiple-access-points"></a>多個存取點
雲端殼層是有彈性的工具，可以從使用：
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 2.0「試試看」文件](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Azure 行動裝置應用程式](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS 程式碼的 Azure 帳戶的擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>連線 Microsoft Azure 檔案儲存體
雲端殼層電腦是暫時性的而且需要 Azure 檔案共用，以裝載為`clouddrive`保存您的檔案。

第一次啟動雲端殼層會提示您建立資源群組、 儲存體帳戶和 Azure 檔案共用您的身分。 這是一次性的步驟，而且會針對所有工作階段自動連接。 單一檔案共用可以進行對應，並同時供 Cloud Shell (預覽) 中的 Bash 和 PowerShell 使用。

#### <a name="create-new-storage"></a>建立新的儲存體
![](media/overview/basic-storage.png)

系統可代替您建立本地備援儲存體 (LRS) 帳戶和「Azure 檔案」共用。 如果您選擇同時使用 Bash 和 PowerShell 環境，「Azure 檔案」共用就會同時用於這兩個環境。 所需成本和一般儲存體相同。

將代替您建立下列三個資源：
1. 名稱如下的資源群組：`cloud-shell-storage-<region>`
2. 名稱如下的儲存體帳戶：`cs<uniqueGuid>`
3. 名稱如下的檔案共用：`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Cloud Shell 中的 Bash 也會建立預設 5-GB 磁碟映像來保存 `$Home`。 $Home 目錄中的所有檔案 (例如 SSH 金鑰) 會都保存於已掛接之 Azure 檔案共用中儲存的使用者磁碟映像中。 在 $Home 目錄和已掛接的 Azure 檔案共用中儲存檔案時，請套用最佳做法。

#### <a name="use-existing-resources"></a>使用現有的資源
![](media/overview/advanced-storage.png)

提供進階選項，將現有資源與 Cloud Shell 建立關聯。
在儲存體設定提示中，按一下 [顯示進階設定] 以顯示其他選項。

> [!Note]
> 下拉式清單會對您的預先指派的雲端殼層地區和 LRS/GRS 的儲存體帳戶篩選。

[了解 Cloud Shell 儲存體、更新 Azure 檔案共用，以及上傳/下載檔案。](persisting-shell-storage.md)

## <a name="concepts"></a>概念
* Cloud Shell 會在以每一工作階段、每位使用者為基礎所提供的暫存主機上執行
* Cloud Shell 會在無互動活動的 20 分鐘後逾時
* Cloud Shell 需要掛接 Azure 檔案共用
* Cloud Shell 會將相同的 Azure 檔案共用同時用於 Bash 和 PowerShell
* Cloud Shell 會以一台機器、一個使用者帳戶的方式指派
* Bash 持續發生 $Home 使用 5 GB 映像存放在檔案共用
* 權限設定為一般 Linux 使用者 (採用 Bash)

深入了解 [Cloud Shell 中的 Bash](features.md) 和 [Cloud Shell (預覽) 中的 PowerShell](features-powershell.md) 中的功能。

## <a name="pricing"></a>價格
裝載 Cloud Shell 的機器是免費提供的，但前提是必須掛接「Azure 檔案」共用。 所需成本和一般儲存體相同。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 中 Bash 的快速入門](quickstart.md) <br>
[Cloud Shell (預覽) 中的 PowerShell 快速入門](quickstart-powershell.md)