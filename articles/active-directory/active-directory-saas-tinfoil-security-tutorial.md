---
title: "教學課程：Azure Active Directory 與 Tinfoil Security 整合 | Microsoft Docs"
description: "了解如何使用 Tinfoil Security 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e3a93cc039d4e24d19f2df2d859c5899cd2a402e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>教學課程：Azure Active Directory 與 Tinfoil Security 整合
本教學課程的目的是要示範 Azure 與 Tinfoil Security 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 已啟用 Tinfoil Security 單一登入功能的訂用帳戶

完成本教學課程之後，您指派給 Tinfoil Security 的 Azure AD 使用者就能夠從您的 Tinfoil Security 公司網站 (識別提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Tinfoil Security 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![設定單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "設定單一登入")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>啟用 Tinfoil Security 的應用程式整合
本節的目的是要說明如何啟用 Tinfoil Security 的應用程式整合。

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>若要啟用 Tinfoil Security 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "應用程式")

4. 按一下頁面底部的 [新增]  。
   
    ![新增應用程式](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "新增應用程式")

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![從資源庫新增應用程式](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "從資源庫新增應用程式")

6. 在**搜尋方塊**中，輸入 **Tinfoil Security**。
   
    ![應用程式資源庫](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "應用程式資源庫")

7. 在結果窗格中，選取 [Tinfoil Security]，然後按一下 [完成] 以新增應用程式。
   
    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 Tinfoil Security。  
設定 Tinfoil Security 的單一登入需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 在 Azure 傳統入口網站的 [Tinfoil Security] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "設定單一登入")

2. 在 [您希望使用者如何登入 Tinfoil Security] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "設定單一登入")

3. 在 [設定應用程式 URL] 頁面的 [Tinfoil Security 回覆 URL] 文字方塊中，輸入您的「Tinfoil Security 判斷提示取用者服務 (ACS) URL」(例如： "*https://www.tinfoilsecurity.com/saml/consume*")，然後按 [下一步]。
   
    > [!NOTE]
    > 您應該能夠從 Tinfoil Security 中繼資料 (https://www.tinfoilsecurity.com/saml/metadata) 取得 ACS URL。
    > 
    > 
   
    ![設定應用程式 URL](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "設定應用程式 URL")

4. 在 [設定在 Tinfoil Security 單一登入] 頁面上，若要下載您的憑證，請按一下 [下載憑證]，然後將憑證檔案以 **c:\\Tinfoil Security.cer** 方式儲存在本機。
   
    ![設定單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "設定單一登入")

5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Tinfoil Security 公司網站。

6. 在頂端工具列中，按一下 [我的帳戶] 。
   
    ![儀表板](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "儀表板")

7. 按一下 [安全性] 。
   
    ![安全性](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "安全性")

8. 在 [單一登入]  組態頁面上，執行下列步驟：
   
    ![單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "單一登入")
   
    a. 選取 [啟用 SAML] 。
   
    b.這是另一個 C# 主控台應用程式。 按一下 [手動設定] 。
   
    c. 在 Azure 傳統入口網站的 [設定在 Tinfoil Security 單一登入] 對話方塊頁面上，複製 [SAML SSO URL] 值，然後將它貼到 [SAML Post URL] 文字方塊中。
   
    d. 從匯出的憑證複製**指紋**值，然後將它貼入 [SAML 憑證指紋] 文字方塊。  
      
    > [!TIP]
    > 如需詳細資訊，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e. 複製 [您的帳戶識別碼] 。
   
    f. 按一下 [儲存] 。

9. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "設定單一登入")

10. 在頂端的功能表中，按一下 [屬性] **屬性** to open the **SAML Token 屬性** 對話方塊。
    
    ![屬性](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "屬性")

11. 若要加入必要的屬性對應，請執行下列步驟：
    
    ![屬性](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "屬性")
    
    a. 按一下 [加入使用者屬性] 。

    b.這是另一個 C# 主控台應用程式。 在 [屬性名稱] 文字方塊中，輸入 **accountid**。

    c. 在 [屬性值]  文字方塊中，貼上前一節已複製的帳戶識別碼值。

    d. 按一下頁面底部的 [新增] 。

12. 按一下 [套用變更] 。

## <a name="configuring-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 Tinfoil Security，則必須將他們佈建到 Tinfoil Security。  
Tinfoil Security 需以手動的方式佈建。

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>若要佈建使用者，請執行下列步驟：
1. 如果該使用者屬於企業帳戶，您需要連絡 Tinfoil Security 支援小組以建立使用者帳戶。
2. 如果該使用者是一般的 Tinfoil Security SaaS 使用者，則可以將合作者加入該使用者的任何網站。 這樣會觸發傳送邀請到指定電子郵件的程序，以建立新的 Tinfoil Security 使用者帳戶。

> [!NOTE]
> 您可以使用任何其他的 Tinfoil Security 使用者帳戶建立工具或 Tinfoil Security 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>若要將使用者指派給 Tinfoil Security，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Tinfoil Security] 應用程式整合頁面上，按一下 [指派使用者]。
   
    ![指派使用者](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "指派使用者")

3. 選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。
   
    ![是](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


