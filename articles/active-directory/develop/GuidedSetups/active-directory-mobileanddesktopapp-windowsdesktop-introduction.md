---
title: "Azure AD v2 Windows Desktop 快速入門 - 簡介 | Microsoft Docs"
description: "Windows Desktop .NET (XAML) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.contentlocale: zh-tw


---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>從 Windows 傳統型應用程式呼叫 Microsoft 圖形 API

本指南示範原生 Windows Desktop .NET (XAML) 應用程式如何取得存取權杖，以及如何呼叫 Microsoft 圖形 API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

在本指南最後，您的應用程式將能夠使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及使用來自使用 Azure Active Directory 之任何公司或組織的公司與學校帳戶呼叫受保護的 API。  

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  沒有嗎？ [免費下載 Visual Studio 2017](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

本指南建立的範例應用程式可讓 Windows 傳統型應用程式查詢 Microsoft 圖形 API，或查詢可接受來自 Azure Active Directory v2 端點之權杖的 Web API。 針對這個案例，系統會透過授權標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者完成驗證之後，範例應用程式就會收到一個權杖，可用來查詢 Microsoft 圖形 API 或由 Microsoft Azure Active Directory v2 保護的 Web API。

像 Microsoft 圖形這樣的 API 需要存取權杖，才能允許存取特定資源，例如讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL 要求存取權杖，以透過指定 API 範圍來存取這些資源。 此存取權杖接著會新增到 HTTP 授權標頭，這些是針對受保護資源發出之每個呼叫的授權標頭。 

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。


### <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|描述|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|


