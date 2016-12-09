---
title: "將 Web App 資源移動到另一個資源群組"
description: "描述您可以將 Web Apps 與 App Service 從一個資源群組移至另一個的案例。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>支援的移動組態
您可以使用 [ARM Move Resources API](../resource-group-move-resources.md)移動Azure Web App 資源。

Azure Web Apps 目前支援下列移動案例：

* 將整個資源群組內容 (Web App、App Service 方案與憑證) 移動到另一個資源群組 
  * 注意：目的地資源群組不能包含此案例中的任何 Microsoft.Web 資源
* 將個別 Web 應用程式移動到不同的資源群組，同時也在其目前的 App Service 方案中裝載它們 (App Service 方案保留在舊的資源群組中)




<!--HONumber=Nov16_HO3-->


