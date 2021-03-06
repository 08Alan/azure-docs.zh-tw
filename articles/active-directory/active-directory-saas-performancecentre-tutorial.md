---
title: "教學課程：Azure Active Directory 與 PerformanceCentre 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 PerformanceCentre 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3b1a347322e2307aadbd287cb2235e39e11f1e79
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>教學課程：Azure Active Directory 與 PerformanceCentre 整合
本教學課程旨在說明如何整合 PerformanceCentre 與 Azure Active Directory (Azure AD)。  

PerformanceCentre 與 Azure AD 整合提供下列優點： 

* 您可以在 Azure AD 中控制可存取 PerformanceCentre 的人員 
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 PerformanceCentre 單一登入 (SSO)
* 您可以在 Azure Active Directory 傳統入口網站集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 PerformanceCentre 整合，您需要以下項目：

* Azure AD 訂用帳戶
* 已啟用 PerformanceCentre 單一登入 (SSO) 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD SSO。  

本教學課程中說明的案例由三個主要部分組成：

*  從資源庫加入 PerformanceCentre 
*  設定並測試 Azure AD SSO

## <a name="add-performancecentre-from-the-gallery"></a>從資源庫加入 PerformanceCentre
若要設定 PerformanceCentre 與 Azure AD 整合，您需要從資源庫將 PerformanceCentre 加入受管理 SaaS 應用程式的清單。

**若要從資源庫加入 PerformanceCentre，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **PerformanceCentre**。
   
    ![應用程式][5]
7. 在結果窗格中，選取 [PerformanceCentre]，然後按一下 [完成] 以新增應用程式。
   
    ![應用程式][500]

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 PerformanceCentre 搭配運作的 Azure AD SSO。

若要讓 SSO 運作，Azure AD 必須知道 PerformanceCentre 中與 Azure AD 互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 PerformanceCentre 中的相關使用者之間，建立連結關聯性。  

建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱] 的值指定為 PerformanceCentre 中 [Username] 的值。

**若要使用 PerformanceCentre 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：**

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 PerformanceCentre 測試使用者](#creating-a-halogen-software-test-user)** - 在 PerformanceCentre 中建立一個 Britta Simon 對應項目，其要與 Azure AD 中代表她的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 PerformanceCentre 應用程式中設定 SSO。

**若要搭配 PerformanceCentre 設定 Azure AD SSO，請執行下列步驟：**

1. 在 Azure AD 傳統入口網站的 [PerformanceCentre] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 PerformanceCentre] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![Azure AD 單一登入][7] 
3. 在 [設定應用程式設定]  對話方塊頁面上，執行下列步驟：
   
    ![Azure AD 單一登入][8] 
   
   1. 在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 PerformanceCentre 網站的 URL (例如：http://companyname.performancecentre.com/saml/SSO)。
   2. 按 [下一步] 。
4. 於 [設定在 PerformanceCentre 單一登入]  頁面上，執行下列步驟：
   
    ![Azure AD 單一登入][9] 
   
  * 按一下 [下載中繼資料]，然後將檔案儲存在您的電腦上。
5. 以系統管理員身分登入您的 **PerformanceCentre** 公司網站。
6. 在左側索引標籤中，按一下 [設定] 。
   
    ![Azure AD 單一登入][10]
7. 在左側索引標籤中，按一下 [其他]，然後按一下 [單一登入]。
   
    ![Azure AD 單一登入][11]
8. 針對 [通訊協定]，選取 [SAML]。
   
    ![Azure AD 單一登入][12]
9. 在記事本中開啟下載的中繼資料檔案，複製其內容，然後貼到 [身分識別提供者中繼資料] 文字方塊，然後按一下 [儲存]。
   
    ![Azure AD 單一登入][13]
10. 確認 [實體基礎 URL]和 [實體識別碼] 的值正確無誤。
    
     ![Azure AD 單一登入][14]
11. 在 Azure AD 傳統入口網站上，選取單一登入設定確認，然後按 [下一步] 。 
    
     ![Azure AD 單一登入][15]
12. 在 [單一登入確認] 頁面上，按一下 [完成]。  
    
     ![Azure AD 單一登入][16]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。  

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟： 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   3. 按 [下一步] 。
   
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟： 
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. 在 [名字] 文字方塊中，輸入 **Britta**。  
   2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   4. 在 [角色] 清單中選取 [使用者]。
   5. 按 [下一步] 。
   
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
   1. 記下 [新密碼] 的值。
   2. 按一下 [完成]。   

### <a name="create-a-performancecentre-test-user"></a>建立 PerformanceCentre 測試使用者
本節的目標是在 PerformanceCentre 中建立名為 Britta Simon 的使用者。

**若要在 PerformanceCentre 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 PerformanceCentre 公司網站。
2. 在左側功能表中，按一下 [內部關聯]，然後按一下 [建立參與者]。
   
    ![建立使用者][400]
3. 在 [內部關聯 - 建立參與者]  對話方塊中，執行下列步驟：
   
    ![建立使用者][401]
   
   1. 在相關的文字方塊中為 Britta Simon 輸入必要的屬性。

    >[!IMPORTANT]
    >在 PerformanceCentre 中，Britta 的使用者名稱屬性必須與 Azure AD 中的使用者名稱相同。
    > 
    > 
 
   2. 對 [選擇角色] 選取 [用戶端系統管理員]。
   3. 按一下 [儲存] 。   


### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是將對 PerformanceCentre 的存取權授與 Britta Simon，使她能夠使用 Azure SSO。

![指派使用者][200] 

**若要指派 Britta Simon 到 PerformanceCentre，請執行以下步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201]
2. 在應用程式清單中，選取 **PerformanceCentre**。
   
    ![指派使用者][202]
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。  

當您在存取面板中按一下 PerformanceCentre 磚時，應該會自動登入您的 PerformanceCentre 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png




