---
title: "使用 FTP/S 將您的應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何使用 FTP 或 FTPS 將您的應用程式部署至 Azure App Service。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: fcd079306a8968505349bb3f4a805f203a5c9999
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>使用 FTP/S 將您的應用程式部署至 Azure App Service

這篇文章說明如何使用 FTP 或 FTPS 將您的 Web 應用程式、行動裝置應用程式後端或 API 應用程式部署到 [Azure App Service (英文)](http://go.microsoft.com/fwlink/?LinkId=529714)。

您應用程式的 FTP/S 端點已經啟動。 啟用 FTP/S 部署不需要任何組態。

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>步驟 1：設定部署認證

若要存取您應用程式的 FTP 伺服器，您必須先部署認證。 

若要設定或重設部署認證，請參閱 [Azure App Service 部署認證](app-service-deployment-credentials.md)。 本教學課程示範如何使用使用者層級的認證。

## <a name="step-2-get-ftp-connection-information"></a>步驟 2：取得 FTP 連線資訊

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟應用程式的[資源頁面](../azure-resource-manager/resource-group-portal.md#manage-resources)。
2. 在左側功能表中選取 [概觀]，然後記下 [FTP/部署使用者]、[FTP 主機名稱] 和 [FTPS 主機名稱] 的值。 

    ![FTP 連線資訊](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > 為了對 FTP 伺服器提供適當的內容，Azure 入口網站所顯示的 [FTP/部署使用者] 值包含了應用程式名稱。
    > 當您在左側功能表中選取 [屬性] 時，可以找到相同資訊。 
    >
    > 此外，絕對不會顯示部署密碼。 如果您忘記您的部署密碼，請回到[步驟 1](#step1) 並重設您的部署密碼。
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>步驟 3︰將檔案部署至 Azure

1. 從您的 FTP 用戶端 (例如 [Visual Studio](https://www.visualstudio.com/vs/community/) 或 [FileZilla](https://filezilla-project.org/download.php?type=client))，使用您所蒐集的連線資訊來連線到您的應用程式。
3. 將您的檔案和其個別的目錄結構複製到 Azure 中的 [**/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (或 WebJobs 的 **/site/wwwroot/App_Data/Jobs/** 目錄)。
4. 瀏覽至您的應用程式 URL，以確認應用程式運作正常。 

> [!NOTE] 
> 與 [Git 型部署](app-service-deploy-local-git.md)不同，FTP 部署不支援下列的部署自動化︰ 
>
> - 相依性還原 (例如 NuGet、NPM、PIP 和編輯器自動化)
> - .NET 二進位檔的編譯
> - web.config 的產生 (此處是 [Node.js 範例](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> 請在本機電腦上手動產生這些必要檔案，然後與您的應用程式一起部署這些檔案。
>
>

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](app-service-deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [Azure App Service 部署認證](app-service-deploy-ftp.md)
