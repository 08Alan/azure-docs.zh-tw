---
title: "最佳做法：Azure AD 密碼管理 | Microsoft Docs"
description: "Azure Active Directory 中密碼管理的部署和使用方式最佳做法、範例使用者文件和訓練指南。"
services: active-directory
documentationcenter: 
author: asteen
manager: femila
editor: curtand
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c2783aabc66eb5eeae7d11a3487307986a61426


---
# <a name="deploying-password-management-and-training-users-to-use-it"></a>部署密碼管理並訓練使用者使用它
> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md)。
> 
> 

啟用密碼重設之後，您必須採取的下一個步驟就是讓使用者使用組織的服務。 若要這麼做，您必須確定已正確將使用者設定為使用此服務，同時您也必須訓練使用者，讓他們可以順利管理自己的密碼。 本文章將向您說明下列概念：

* [**如何針對密碼管理設定使用者**](#how-to-get-users-configured-for-password-reset)
  * [如何針對密碼重設設定帳戶](#what-makes-an-account-configured)
  * [自行填入驗證資料的方式](#ways-to-populate-authentication-data)
* [**向組織推行密碼重設的最佳方式**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [以電子郵件為基礎的啟用與範例電子郵件通訊](#email-based-rollout)
  * [針對您的使用者建立您自己的自訂密碼管理入口網站](#creating-your-own-password-portal)
  * [如何使用強制註冊來強制使用者在登入時註冊](#using-enforced-registration)
  * [如何上傳使用者帳戶的驗證資料](#uploading-data-yourself)
* [**範例使用者和支援訓練教材 (即將推出！)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>如何針對密碼重設設定使用者
本節說明各種您可以使用的方法，以確保組織中的每位使用者均能在忘記密碼時，有效地使用自助密碼重設。

### <a name="what-makes-an-account-configured"></a>如何設定帳戶
使用者能夠使用密碼重設之前，必須符合下列 **所有** 條件：

1. 必須在目錄中啟用密碼重設。  若要了解如何啟用密碼重設，請參閱[讓使用者重設其 Azure AD 密碼](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)或[讓使用者重設或變更其 AD 密碼](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2. 使用者必須獲得授權。
   * 針對雲端使用者，使用者必須具有**任何付費的 Office 365 授權**，或被指派 **AAD Basic 授權**或 **AAD Premium 授權**。
   * 若為內部部署使用者 (已同盟或已雜湊同步處理)，則這類使用者 **必須具有指派的 AAD Premium 授權**。
3. 使用者必須具有 **一組最基本的已定義驗證資料** ，且資料符合目前的密碼重設原則。
   * 如果目錄中的對應欄位含有格式正確的資料，就會將驗證資料視為已定義。
   * 如果設定了單一閘道原則，就會將一組最基本的驗證資料定義為已啟用之驗證選項中的**至少其中一項**；或者，如果設定了兩個閘道原則，則會定義為已啟用之驗證選項中的**至少其中兩項**。
4. 如果使用者使用內部部署帳戶，就必須啟用並開啟 [密碼回寫](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) 。

### <a name="ways-to-populate-authentication-data"></a>填入驗證資料的方式
您有幾個選項可用於指定要用於密碼重設的組織中使用者資料。

* 在 [Azure 管理入口網站](https://manage.windowsazure.com)或 [Office 365 系統管理入口網站](https://portal.microsoftonline.com)中編輯使用者
* 使用 Azure AD Sync 從內部部署 Active Directory 網域將使用者屬性同步處理至 Azure AD
* 使用 Windows PowerShell 並透過 [遵循下列步驟](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)，來編輯使用者屬性。
* 引導使用者至位於 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* 將 [[要求使用者在登入時註冊嗎?](active-directory-passwords-customize.md#require-users-to-register-when-signing-in)] 組態選項設定為 [是]，以在使用者登入其 Azure AD 帳戶時，要求使用者註冊密碼重設。

使用者不需要註冊密碼重設，系統也能運作。  舉例來說，如果您在本機目錄中有現有的行動電話號碼或辦公室電話號碼，就可以在 Azure AD 中同步處理這些號碼，然後我們會自動將這些號碼用於密碼重設。

您也可以進一步了解[密碼重設如何使用資料](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)和[如何使用 PowerShell 填入個別驗證欄位](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)。

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>為使用者推行密碼重設的最佳方式？
推行密碼重設的一般步驟如下：

1. 移至 [Azure 管理入口網站](https://manage.windowsazure.com)中的 [設定] 索引標籤，然後針對 [啟用密碼重設的使用者] 選項選取 [是]，以在您的目錄中啟用密碼重設。
2. 移至 [Azure 管理入口網站](https://manage.windowsazure.com)中的 [授權] 索引標籤，將適當的授權指派給您想要提供密碼重設的每位使用者。
3. (選擇性) 透過將 [限制密碼重設的存取] 切換至 [是]，並選取要啟用密碼重設功能的安全性群組，將密碼重設限制在一組使用者來隨時間緩慢推行這項功能 (請注意這些使用者必須都已被指派授權)。
4. 指示使用者使用密碼重設的方式包括：傳送電子郵件指示使用者註冊、在存取面板上啟用強制註冊，或是透過 DirSync、PowerShell 或 [Azure 管理入口網站](https://manage.windowsazure.com)自行將這些使用者的適當驗證資料上傳。  下方提供此作業的詳細說明。
5. 在一段時間過後，瀏覽至 [報告] 索引標籤並檢視[**密碼重設登錄活動**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity)報告，以查閱使用者的註冊情形。
6. 在有許多使用者註冊之後，瀏覽至 [報告] 索引標籤，並檢視[**密碼重設活動**](active-directory-passwords-get-insights.md#view-password-reset-activity)報告，以觀察他們使用密碼重設的情形。

有幾種方式可以通知使用者，讓他們知道註冊後就能使用組織的密碼重設。  這些方式詳述如下。

### <a name="email-based-rollout"></a>以電子郵件為基礎的啟用
這或許是通知使用者的最簡單方式，您只要傳送電子郵件，指示他們註冊或使用密碼重設即可。  以下是可用來執行這項作業的範本。  您可以選擇用自己的顏色/標誌來取代範本的的顏色/標誌，藉此自訂範本以符合使用需求。

  ![][001]

您可以 [在這裡下載電子郵件範本](http://1drv.ms/1xWFtQM)。

### <a name="creating-your-own-password-portal"></a>建立您自己的密碼入口網站
針對部署密碼管理功能的大型客戶，其中一種策略就是建立一個使用者可以在單一位置管理所有與其密碼相關之項目的單一「密碼入口網站」。  

我們許多最大客戶都選擇建立根 DNS 項目 (例如 https://passwords.contoso.com)，此項目中帶有 Azure AD 密碼重設入口網站、密碼重設註冊入口網站及密碼變更頁面的連結。  如此一來，您送出的任何電子郵件或傳單都可以包含一個單一、令人印象深刻，且使用者能開始使用該服務時可以前往的 URL。

為了此處的進行，我們建立了一個使用最新回應 UI 設計架構、且能夠在所有的瀏覽器與行動裝置上運作的簡易頁面。

  ![][007]

您可以 [在這裡下載網站範本](https://github.com/kenhoff/password-reset-page)。  建議您針對貴組織的需求自訂標誌和色彩。

### <a name="using-enforced-registration"></a>使用強制註冊
如果您想讓使用者自行註冊密碼重設，也可以強制這些使用者在登入位於 [http://myapps.microsoft.com](http://myapps.microsoft.com)的存取面板時註冊。  您可以從目錄的 [設定] 索引標籤中，藉由啟用 [要求使用者必須在登入存取面板時註冊嗎] 選項來啟用此選項。  

您可以選擇性地將 [使用者必須確認連絡資料之前的天數]  選項修改為非零的值，以定義是否要在經過一段可設定的時間後，要求使用者重新註冊。 如需詳細資訊，請參閱 [自訂使用者密碼管理行為](active-directory-passwords-customize.md#password-management-behavior) 。

  ![][002]

啟用此選項後，使用者如果登入存取面板，就會看到快顯視窗，通知他們系統管理員已要求他們確認自己的連絡資訊。 若這些使用者失去帳戶的存取權時，便可使用該視窗來重設密碼。

  ![][003]

按一下 [立即驗證] 就會將他們帶往位於 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) 的**密碼重設註冊入口網站**，並要求他們註冊。  按一下 [取消]  按鈕或關閉視窗，即可取消透過這種方式註冊，但如果使用者不註冊，就會在每次登入時收到提醒。

  ![][004]

### <a name="uploading-data-yourself"></a>自行上傳資料
如果想要自行上傳驗證資料，那使用者就不需要註冊密碼重設，即可重設他們的密碼。  只要使用者的帳戶中有驗證資料，且資料符合您定義的密碼重設原則，那麼這些使用者就能重設密碼。

若要了解您可以透過 AAD Connect 或 Windows PowerShell 設定哪些屬性，請參閱 [密碼重設使用哪些資料](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)。

您可以依照下列步驟，透過 [Azure 管理入口網站](https://manage.windowsazure.com) 上傳驗證資料：

1. 在 **Azure 管理入口網站** 的 [Active Directory 延伸模組](https://manage.windowsazure.com)中瀏覽至您的目錄。
2. 按一下 [使用者]  索引標籤。
3. 從清單中選取您需要的使用者。
4. 您會在第一個索引標籤上發現 **備用電子郵件**項目，此項目可做為啟用密碼重設的屬性。
   
   ![][005]
5. 按一下 [工作資訊]  索引標籤。
6. 在此頁面上，您會發現有 [辦公室電話]、[行動電話]、[驗證電話] 及 [驗證電子郵件]。  您也可以設定這些屬性，以允許使用者重設密碼。
   
   ![][006]

請參閱 [密碼重設使用哪些資料](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) ，了解這些屬性的使用方式。

請參閱 [如何從 PowerShell 為您的使用者存取密碼重設資料](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) ，了解您如何使用 PowerShell 讀取和設定這項資料。

## <a name="sample-training-materials"></a>範例訓練教材
我們正在準備範例訓練教材，讓您的 IT 部門和使用者都能快速了解如何部署及使用密碼重設。  敬請期待！

<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>密碼重設文件的連結
以下是所有 Azure AD 密碼重設文件頁面的連結：

* **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md)。
* [**運作方式**](active-directory-passwords-how-it-works.md) - 了解六個不同的服務元件及其功能
* [**開始使用**](active-directory-passwords-getting-started.md) - 了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) - 了解如何依照組織的需求自訂外觀和服務行為
* [**深入探索**](active-directory-passwords-get-insights.md) - 了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) - 取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何快速移難排解服務的問題
* [**深入了解**](active-directory-passwords-learn-more.md) - 深入探索服務運作方式的技術細節

[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"



<!--HONumber=Nov16_HO3-->


