---
title: "教學課程︰以 Azure Active Directory 設定 Pingboard 來自動佈建使用者 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 將使用者帳戶自動佈建和取消佈建至 Pingboard。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>教學課程︰設定 Pingboard 來自動佈建使用者

本教學課程旨在說明您需要執行的步驟，以將使用者帳戶從 Azure Active Directory 自動佈建和取消佈建至 Pingboard。

## <a name="prerequisites"></a>先決條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶
*   Pingboard 租用戶 [Pro 帳戶](https://pingboard.com/pricing) 
*   Pingboard 中具有管理員權限的使用者帳戶 

> [!NOTE] 
> Azure Active Directory 佈建整合依存於可供您帳戶使用的 Pingboard API (`https://your_domain.pingboard.com/scim/v2`)。

## <a name="assigning-users-to-pingboard"></a>將使用者指派給 Pingboard

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動佈建使用者帳戶的內容中，只有「已指派」至 Azure Active Directory 中應用程式的使用者才會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure Active Directory 中的哪些使用者是代表需要 Pingboard 應用程式存取權的使用者。 一旦決定後，您可以遵循此處的指示，將這些使用者指派給 Pingboard 應用程式︰

[將使用者指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>將使用者指派給 Pingboard 的重要秘訣

*   建議您將單一 Azure Active Directory 使用者指派給 Pingboard 來測試佈建設定。 其他使用者可能會稍後再指派。

## <a name="configuring-user-provisioning-to-pingboard"></a>設定將使用者佈建至 Pingboard 

本節會引導您將 Azure Active Directory 連線至 Pingboard 使用者帳戶佈建 API，並以 Azure Active Directory 中的使用者指派為基礎設定佈建服務，以在 Pingboard 中建立、更新和停用指派的使用者帳戶。

> [!TIP]
> 您也可以選擇啟用 Pingboard 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>若要在 Azure Active Directory 中設定將使用者帳戶自動佈建至 Pingboard：

1)  在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2) 如果您已設定單一登入的 Pingboard，請使用 [搜尋] 欄位搜尋您的 Pingboard 執行個體。 否則，請選取 [新增]，並在應用程式資源庫中搜尋 [Pingboard]。 從搜尋結果中選取 [Pingboard]，並將它新增至您的應用程式清單。

3)  選取您的 Pingboard 執行個體，然後選取 [佈建] 索引標籤。

4)  將 [佈建模式] 設定為 [自動]。

![Pingboard 佈建](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) 在 [管理員認證] 區段底下，執行下列步驟：

* 在 [租用戶 URL] 文字方塊中輸入 `https://your_domain.pingboard.com/scim/v2`，並將 your_domain 取代為您實際的網域
* 使用系統管理員帳戶登入 [Pingboard](https://pingboard.com/)。
* 按一下 [附加元件] > [整合] > [Azure Active Directory]。
* 按一下 [設定] 索引標籤，然後選取 [啟用從 Azure 進行使用者佈建]。
* 複製 [OAuth 持有人權杖] 欄位，並輸入 [祕密權杖] 文字方塊中。

6) 在 Azure 入口網站中，按一下 [測試連線] 以確保 Azure Active Directory 可以連線到您的 Pingboard 應用程式。 如果連線失敗，請確定您的 Pingboard 帳戶具有系統管理員權限，並再試一次「測試連線」步驟。

7) 在 [通知電子郵件] 欄位中輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選下列核取方塊。

8) 按一下 [檔案] 。 

9) 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Pingboard]。

10) 在 [屬性對應] 區段中，檢閱將從 Azure Active Directory 同步處理至 Pingboard 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Pingboard 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。 如需詳細資訊，請參閱[自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)

11) 若要啟用 Pingboard 的 Azure Active Directory 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]

12) 按一下 [儲存] 以啟動對指派給 Pingboard 的使用者進行首次同步處理。

首次同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘就會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，當中會描述您 Pingboard 應用程式上的佈建服務所執行之所有動作。

如需如何讀取 Azure Active Directory 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-enterprise-apps-manage-provisioning.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-pingboard-tutorial.md)
