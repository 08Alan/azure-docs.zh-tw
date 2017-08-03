---
title: "在 Linux 上使用 Azure Web 應用程式連續部署 | Microsoft Docs"
description: "如何在 Linux 的 Azure Web 應用程式中設定連續部署。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 6b6c173c6c4bb3f670c54208c80e6d966a1f396e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/19/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>在 Linux 上使用 Azure Web 應用程式連續部署

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

在本教學課程中，您可以從受控的 [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) 存放庫或 [Docker 中樞](https://hub.docker.com)設定自訂容器映像的連續部署。

## <a name="step-1---sign-in-to-azure"></a>步驟 1 - 登入 Azure

登入 Azure 入口網站 (網址是 http://portal.azure.com)

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>步驟 2 - 啟用 Docker 中樞連續部署

在 **[Azure 入口網站](https://portal.azure.com/)**中，按一下頁面左側的 [App Service] 選項。

按一下您想要設定 Docker 中樞連續部署的應用程式名稱。

在 [應用程式設定] 中，新增名為 `DOCKER_ENABLE_CI` 且包含 `true` 值的應用程式設定。

![將應用程式設定的影像插入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>步驟 3 - 準備 Webhook URL

針對 Webhook URL，您必須擁有下列端點︰`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

您可以取得 `publishingusername` 和 `publishingpwd`，方法是使用 Azure 入口網站來下載 Web 應用程式發佈設定檔。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>步驟 4：新增 Web Hook

### <a name="azure-container-registry"></a>Azure Container Registry

在您的登錄入口網站刀鋒視窗中，按一下 [Webhook]，按一下 [新增] 以建立新的 webhook。 在 [建立 webhook] 刀鋒視窗中，提供您的 webhook 名稱。 在 Webhook URI 中，您需要提供從**步驟 3** 取得的 URL。

請確定您將範圍定義為包含您的容器映像的存放庫。

![插入 webhook 的映像](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

當更新映像時，Web 應用程式會以新的映像自動進行更新。

### <a name="docker-hub"></a>Docker 中樞

在您的 [Docker 中樞] 頁面上，依序按一下 [Webhook]、[建立 WEBHOOK]。

![將新增 webhook 1 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

在 Webhook URL 中，您需要提供從**步驟 3** 取得的 URL。

![將新增 webhook 2 的映像插入](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

當更新映像時，Web 應用程式會以新的映像自動進行更新。

## <a name="next-steps"></a>後續步驟
* [什麼是 Linux 上的 Azure Web 應用程式？](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 應用程式中建立應用程式](./app-service-linux-how-to-create-web-app.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [在 Linux 上的 Azure Web 應用程式中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure Web 應用程式中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure Web 應用程式中使用 Ruby](app-service-linux-ruby-get-started.md)
* [如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像](./app-service-linux-using-custom-docker-image.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](./app-service-linux-faq.md) 




