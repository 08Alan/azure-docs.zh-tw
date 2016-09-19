<properties 
    pageTitle="教學課程：Azure Active Directory 與 MCM 整合 | Microsoft Azure" 
    description="了解如何使用 MCM 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#教學課程：Azure Active Directory 與 MCM 整合
  
本教學課程旨在說明如何整合 MCM 與 Azure Active Directory (Azure AD)。

MCM 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 MCM
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 MCM (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 MCM 整合，您需要下列項目：

- 有效的 Azure 訂閱
- 已啟用 MCM 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 MCM
2. 設定並測試 Azure AD 單一登入

## 從資源庫新增 MCM
若要設定將 MCM 整合到 Azure AD 中，您需要從資源庫將 MCM 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 MCM，請執行下列步驟：**

1.  在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "應用程式")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從資源庫新增應用程式]。

    ![從資源庫新增應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "從資源庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **MCM**。

    ![應用程式庫](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "應用程式庫")

7.  在結果窗格中，選取 [MCM]，然後按一下 [完成] 新增應用程式。

    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 MCM 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 MCM 與 Azure AD 中互相對應的使用者。換句話說，必須建立 Azure AD 使用者和 MCM 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中 [使用者名稱] 的值指定為 MCM 中 **Username** 的值。

若要設定及測試與 MCM 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 MCM 測試使用者](#creating-a-mcm-test-user)** - 在 MCM 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入
  
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，然後在您的 MCM 應用程式中設定單一登入。

**若要設定與 MCM 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1.  在 Azure 傳統入口網站的 [MCM] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "設定單一登入")

2.  在 [要如何讓使用者登入 MCM] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD 單一登入")

3.  在 [設定 App 設定] 對話方塊頁面執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "設定應用程式 URL")

    a.在 [登入 URL] 文字方塊中，輸入 `https://myaba.co.uk/client-access/<company name>/saml.php`。
	
	b. 按一下 [下一步]

4.  在 [設定在 MCM 單一登入] 頁面上，按一下 [下載中繼資料]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "設定單一登入")

5. 若要為您的應用程式設定 SSO，請連絡 MCM 支援小組。附加下載的中繼資料檔案，並與 Bynder 小組共用，以便在 MCM 端設定 SSO。

6.  在傳統入口網站中，選取單一登入設定確認項目，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "設定單一登入")

7. 在 [單一登入確認] 頁面上，按一下 [完成]。

	![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "設定單一登入")


### 建立 Azure AD 測試使用者

本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在「Azure 傳統入口網站」中，按一下左方瀏覽窗格上的 [Active Directory]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表中的 [使用者]。
    
	![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. 若要開啟 [加入使用者] 對話方塊，請按一下底部工具列中的 [加入使用者]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    a.針對 [使用者類型]，選取 [您組織中的新使用者]。

    b.在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。

    c.按 [下一步]。

6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：
    
	![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    a.在 [名字] 文字方塊中，輸入 **Britta**。

    b.在 [姓氏] 文字方塊中，輸入 **Simon**。

    c.在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。

    d.在 [角色] 清單中選取 [使用者]。

    e.按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
    
	![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：
    
	![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    a.記下 [新密碼] 的值。

    b.按一下 [完成]。

###建立 MCM 測試使用者
  
在本節中，您要在 MCM 中建立名為 Britta Simon 的使用者。請與 MCM 支援小組合作，在 MCM 平台中新增使用者。

>[AZURE.NOTE]您可以使用任何其他的 MCM 使用者帳戶建立工具或 MCM 提供的 API，佈建 AAD 使用者帳戶。


###指派 Azure AD 測試使用者
  
本節的目標是授與 Britta Simon 對 MCM 的存取權，讓她能夠使用 Azure 單一登入。
	
![指派使用者](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "指派使用者")

**若要將 Britta Simon 指派給 MCM，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。
    
	![指派使用者](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "指派使用者")

2. 在應用程式清單中，選取 [MCM]。
    
	![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. 在頂端的功能表中，按一下 [使用者]。
    
	![指派使用者](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "指派使用者")

1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部的工具列中，按一下 [指派]。
    
	![指派使用者](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "指派使用者")


### 測試單一登入

本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。
 
當您在存取面板中按一下 [MCM] 圖格時，應該會自動登入您的 MCM 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0907_2016-->