---
title: 應用程式同意如何運作 | Microsoft Docs
description: 深入了解 Azure AD 同意架構的運作方式，以了解如何在開發搭配 Azure AD 執行的應用程式時使用它
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: d8a475bad5c232ceb7553674d97ad8abdb89ae5b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52421669"
---
# <a name="how-application-consent-works"></a>應用程式同意如何運作

此文章旨在協助您深入了解 Azure AD 同意架構的運作方式，以便您可以更有效地開發應用程式。

## <a name="recommended-documents"></a>建議的文件

- 取得有關[同意如何讓資源擁有者控管應用程式對資源的存取權](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent)的基本知識。
- 取得有關 [Azure AD 同意架構如何實作同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)的逐步概觀。
- 深入了解[多租用戶應用程式如何使用同意架構](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)來實作「使用者」與「系統管理員」同意，進而支援更進階的多層應用程式模式。
- 深入了解[授權碼授與流程期間 OAuth 2.0 通訊協定層如何支援同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)。

## <a name="next-steps"></a>後續步驟
[AzureAD StackOverflow (英文)](https://stackoverflow.com/questions/tagged/azure-active-directory)
