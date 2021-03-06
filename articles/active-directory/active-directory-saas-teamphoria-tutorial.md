---
title: "教學課程：Azure Active Directory 與 Teamphoria 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Teamphoria 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 2a35efb04d7fe22abc6894c149caf090666ce016
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>教學課程：Azure Active Directory 與 Teamphoria 整合

在本教學課程中，您會了解如何整合 Teamphoria 與 Azure Active Directory (Azure AD)。

Teamphoria 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Teamphoria 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Teamphoria (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>必要條件

如要設定 Azure AD 與 Teamphoria 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 一個已啟用 Teamphoria 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Teamphoria
2. 設定並測試 Azure AD 單一登入

## <a name="adding-teamphoria-from-the-gallery"></a>從資源庫新增 Teamphoria
若要設定將 Teamphoria 整合到 Azure AD 中，您需要從資源庫將 Teamphoria 新增到受管理的 SaaS app 清單。

**若要從資源庫新增 Teamphoria，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **Teamphoria**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. 在結果窗格中，選取 [Teamphoria]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Teamphoria 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Teamphoria 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Teamphoria 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是指派 Azure AD 中**使用者名稱**的值作為 Teamphoria 中 **Username** 的值。

若要設定及測試與 Teamphoria 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Teamphoria 測試使用者](#creating-a-teamphoria-test-user)** - 使 Teamphoria 中，Britta Simon 的對應使用者能夠連結到代表她在 Azure AD 中的項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 Teamphoria 應用程式中設定單一登入。

**若要設定與 Teamphoria 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [Teamphoria] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. 在 [Teamphoria 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL：`https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > 請注意這些不是真正的值。 您必須以實際的登入 URL 更新這些值。 請連絡 [Teamphoria 用戶端支援小組](https://www.teamphoria.com/)以取得登入 URL。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. 在 [Teamphoria 組態] 區段上，按一下 [設定 Teamphoria] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. 若要在 **Teamphoria** 端設定單一登入，請以系統管理員身分登入 Teamphoria 應用程式。

8. 移至左側工具列中的 [系統管理員設定] 選項，然後在 [設定] 索引標籤上按一下 [單一登入] 以開啟 SSO 組態視窗。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. 按一下右上角的 [新增識別提供者] 選項來開啟表單，以供新增 SSO 的設定。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. 在欄位中輸入詳細資料，如下所示

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **顯示名稱**：在 [系統管理] 頁面輸入外掛程式的顯示名稱。

    b. **按鈕名稱**︰索引標籤的名稱，會在用於透過 SSO 登入的登入頁面上顯示。

    c. **憑證**︰在記事本中開啟您稍早從 Azure 入口網站下載的憑證，複製相同的內容並貼到這裡的方塊中。

    d. **進入點**︰貼上稍早從 Azure 入口網站複製的 **SAML 單一登入服務 URL**。

    e. 將選項切換為 [開啟]，然後按一下 [儲存]。    

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-a-teamphoria-test-user"></a>建立 Teamphoria 測試使用者

若要讓 Azure AD 使用者可以登入 Teamphoria，則必須將他們佈建到 Teamphoria。 Teamphoria 需以手動的方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Teamphoria 公司網站。

2. 按一下左側工具列上的 [系統管理員] 設定，然後在 [管理] 索引標籤底下按一下 [使用者]，以開啟使用者的 [系統管理] 頁面。

    ![新增員工](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. 按一下 [手動邀請] 選項。

    ![邀請人員](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)    

4. 在這個頁面上執行下列動作。 
    
    ![邀請人員](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)    

    a. 在 [電子郵件地址] 文字方塊中，輸入 Britta Simon 的**電子郵件地址**。

    b.這是另一個 C# 主控台應用程式。 在 [名字] 文字方塊中，輸入 **Britta**。

    c. 在 [姓氏] 文字方塊中，輸入 **Simon**。

    d. 按一下 [邀請 1 位使用者]。 使用者必須接受邀請才能建立到系統中。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Teamphoria 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**如要將 Britta Simon 指派給 Teamphoria，請執行下列步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Teamphoria]。

    ![設定單一登入](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](https://msdn.microsoft.com/library/dn308586)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png


