---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "58115156"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

若要註冊 Web API，請使用表格中所指定的設定。

![新 Web API 的範例註冊設定](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| 設定      | 範例值  | 描述                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名稱** | Contoso B2C API | 輸入應用程式的 [名稱]，此名稱可為取用者說明您的 API。 | 
| **包含 Web 應用程式 / Web API** | 是 | 針對 Web API 選取 [是]。 |
| **允許隱含流程** | 是 | 如果您的應用程式使用 [OpenID Connect 登入](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)，請選取 [是] |
| **回覆 URL** | `https://localhost:44316/` | 回覆 URL 是 Azure AD B2C 傳回您應用程式要求之任何權杖的所在端點。 在此範例中，您的 Web API 位於本機並在通訊埠 44316 上進行接聽。 |
| **應用程式識別碼 URI** | api | 應用程式識別碼 URI 是您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 |

按一下 [建立]  以註冊您的應用程式。

新註冊的應用程式會顯示在 B2C 租用戶的應用程式清單中。 從清單中選取 Web API。 API 的 [屬性] 窗格隨即顯示。

![Web API 屬性](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

請記下全域唯一的 [應用程式用戶端識別碼]。 您可在您的應用程式程式碼中使用此識別碼。