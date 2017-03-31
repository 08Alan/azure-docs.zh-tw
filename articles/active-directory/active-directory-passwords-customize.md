---
title: "自訂：Azure Active Directory 密碼管理 | Microsoft Docs"
description: "如何自訂 Azure AD 中的密碼管理外觀、行為和通知，以符合您的需求。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 2cddd150-8747-447a-a7cf-1d7d5775c0b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 06d94b4f0f2c5cc979fe7ecb061fee7d8dde8972
ms.lasthandoff: 03/28/2017


---
# <a name="customizing-password-management-to-fit-your-organizations-needs"></a>自訂密碼管理以符合您的組織的需求
> [!IMPORTANT]
> **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md#reset-your-password)。
>
>

為了給予您的使用者最佳體驗，我們建議您探索和嘗試可以使用的所有密碼管理組態選項。 事實上，您可以前往 **Azure 傳統入口網站** 的 [Active Directory 延伸模組](https://manage.windowsazure.com)中的組態索引標籤，立即開始探索。 本主題會引導您在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，從您的目錄的 [設定] 索引標籤，完成您以系統管理員身分所能做到的各種密碼管理自訂。

## <a name="what-customization-options-are-available"></a>有哪些自訂選項可用？
下表概述所有可用於 Azure Active Directory 密碼重設的自訂選項。

| 主題 | 設定 | 需要的授權 |
| --- | --- | --- |
| 如何啟用或停用密碼重設？ |[設定：啟用使用者的密碼重設](#users-enabled-for-password-reset) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何將密碼重設的範圍限定為一組特定的使用者？ |[將密碼重設限定於特定使用者](#restrict-access-to-password-reset) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何變更支援的驗證方法？ |[設定：使用者可用的驗證方法](#authentication-methods-available-to-users) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何變更必要驗證方法數目？ |[設定：必要驗證方法數目](#number-of-authentication-methods-required) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何設定自訂安全性問題？ |[設定：自訂安全性問題](#custom-security-questions) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何設定預先定義的當地語系化安全性問題？ |[設定：以知識為基礎的安全性問題](#knowledge-based-security-questions) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何變更必要的安全性問題數目？ |[設定：註冊或重設的安全性問題數目](#number-of-questions-required-to-register) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何促使我的使用者在登入時註冊？ |[以強制註冊的方式推動密碼重設](#require-users-to-register-when-signing-in) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何促使我的使用者定期重新確認他們的註冊？ |[設定：幾天後使用者必須重新確認其驗證資料](#number-of-days-before-users-must-confirm-their-contact-data) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何自訂使用者聯繫系統管理員的方式？ |[設定：自訂「連絡您的系統管理員」連結](#customize-the-contact-your-administrator-link) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何根據雲端管理經驗來啟用或停用密碼回寫？ |[設定：啟用或停用密碼回寫](#write-back-passwords-to-on-premises-directory) | <ul><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何讓使用者直接解除鎖定內部部署 AD 帳戶，而不必重設密碼？ |[設定：讓使用者直接解除鎖定 AD 帳戶而不必重設密碼](#allow-users-to-unlock-accounts-without-resetting-their-password) | <ul><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何為使用者啟用密碼重設通知？ |[設定：在使用者的密碼重設時通知使用者](#notify-users-and-admins-when-their-own-password-has-been-reset) |  <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何為系統管理員啟用密碼重設通知？ |[設定：在系統管理員重設其密碼時通知其他系統管理員](#notify-admins-when-other-admins-reset-their-own-passwords) | <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |
| 如何自訂密碼重設的外觀及操作？ |[設定：公司名稱、商標和標誌 ](#password-management-look-and-feel) |  <ul><li>O365 (任何付費 SKU) [僅限雲端使用者]</li><li>Azure AD Basic [僅限雲端使用者]</li><li>Azure AD Premium P1 或 P2 [雲端或內部部署使用者]</li><li>Enterprise Mobility Suite [雲端或內部部署使用者]</li><li>Enterprise Cloud Suite [雲端或內部部署使用者]</li></ul> |

## <a name="password-management-look-and-feel"></a>密碼管理外觀與風格
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。  您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [設定] 索引標籤的 [目錄內容] 區段底下，設定這些選項。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>目錄名稱</p>
                </div>
              </td>
              <td>
                <p>決定使用者或系統管理員會在密碼重設電子郵件通訊上看到什麼組織名稱</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>「連絡您的系統管理員」電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定寄件地址的易記名稱，例如，「Microsoft 代表 <strong>Wingtip Toys</strong>」<br><br></li>
                  <li class="unordered">
決定電子郵件的主旨名稱，例如，<strong>「Wingtip Toys</strong> 帳戶電子郵件驗證碼」<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定寄件地址的易記名稱，例如，「Microsoft 代表 <strong>Wingtip Toys</strong>」<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>登入和存取面板頁面外觀</p>
                </div>
              </td>
              <td>
                <p>決定造訪密碼重設頁面的使用者是否會看到 Microsoft 標誌或您自己的自訂標誌。  這個組態項目也會將您的商標加入至存取面板和登入頁面。</p>
                <p>您可以在<a href="https://technet.microsoft.com/library/dn532270.aspx">將公司商標新增至您的登入和存取面板頁面</a>深入了解租用戶商標和自訂功能。</p>
                                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定在密碼重設入口網站頂端顯示您的標誌，而不是預設 Microsoft 標誌。<br><br>
                    <strong>附註：</strong>如果您直接進入密碼重設頁面，可能無法在密碼重設入口網站的第一頁上看見您的標誌。 一旦使用者輸入使用者名稱並按 [下一步]，您的標誌就會出現。<br><br>
您可以將 <code>whr</code> 參數傳遞至密碼重設頁面，以強制在頁面載入時顯示您的標誌，如下所示︰<code><a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a></code><br><br>
您可以傳遞 <code>username</code> 參數，以產生連結來預先填入使用者名稱欄位。 這樣也會載入您組織的標誌 (如果已設定)︰<code><a href="https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com">https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com</a></code></li>
                </ul>
                <p>
                  <strong>「連絡您的系統管理員」電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定當使用者選擇按一下密碼重設 UI 上的「連絡您的系統管理員」連結連絡您的時候，是否在傳送給系統管理員的電子郵件底端顯示您的標誌。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定當使用者重設其密碼時，是否在傳送給使用者的電子郵件底端顯示您的標誌。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-behavior"></a>密碼管理行為
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。  您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [設定] 索引標籤的 [使用者密碼重設原則] 區段底下，設定這些選項。

> [!NOTE]
> 您使用的系統管理員帳戶必須已獲指派 AAD Premium 授權，才能看到這些原則控制項。<br><br>這些原則控制項只適用於使用者重設其密碼，不適用於系統管理員。  **系統管理員擁有備用電子郵件和/或行動電話的預設原則，由 Microsoft 為其指定且無法變更。**
>
>

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>使用者已啟用密碼重設</p>
                </div>
              </td>
              <td>
                <p>判對是否對此目錄中的使用者啟用密碼重設。 </p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，則沒有使用者可以註冊自己的挑戰資料。<br><br></li>
                  <li class="unordered">
如果設定為是，則目錄中的任何使用者都可以前往位於 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> 的註冊入口網站，註冊挑戰資料。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者必須擁有受指派的 Azure AD Premium 或 Basic 授權，才可以註冊密碼重設。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，使用者會看到訊息，指出必須連絡系統管理員重設其密碼。<br><br></li>
                  <li class="unordered">
如果設定為是，使用者就能夠自動重設其密碼，方法是移至 <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>，或按一下任何組織識別碼登入頁面上的 [無法存取您的帳戶]<strong></strong> 連結。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者必須擁有受指派的 Azure AD Premium 或 Basic 授權，才可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>密碼重設限制存取</p>
                </div>
              </td>
              <td>
                <p>決定是否只允許特定群組使用者使用密碼重設。 (只有在 [使用者啟用密碼重設]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
此設定不會影響使用者的密碼重設註冊入口網站的存取權。 如果 [使用者已啟用密碼重設]<strong></strong> 設定為 [是]<strong></strong>，則在您的目錄中的所有使用者都可以在下列位置註冊密碼重設：<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>。
                  </li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，則在您的目錄中的所有使用者都可以重設其密碼。<br><br></li>
                  <li class="unordered">
如果設定為是，則只有在 [可以執行密碼重設的群組]<strong></strong> 控制項中指定的使用者可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>可以執行密碼重設的群組</p>
                </div>
              </td>
              <td>
                <p>決定允許哪個使用者群組使用密碼重設。 </p>
                <p>(只有在 [密碼重設限制存取]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果未指定任何群組，而且您按一下 [儲存]<strong></strong>，則會為您建立稱為 <strong>SSPRSecurityGroupUsers</strong> 的空群組。<br><br></li>
                  <li class="unordered">
如果您想要指定自己的群組，可以提供自己的顯示名稱。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
此設定不會影響使用者的密碼重設註冊入口網站的存取權。 如果 [使用者已啟用密碼重設]<strong></strong> 設定為 [是]<strong></strong>，則在您的目錄中的所有使用者都可以在下列位置註冊密碼重設：<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>。
                  </li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果 [密碼重設限制存取]<strong></strong> 設為 [是]<strong></strong>，則只有這個群組中的使用者能夠重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>使用者可用的驗證方法</p>
                </div>
              </td>
              <td>
                <p>決定允許使用者使用哪些挑戰以重設其密碼。</p>
                <p>(只有在 [使用者啟用密碼重設]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
必須選取至少一個選項。<br><br></li>
                  <li class="unordered">
我們強烈建議您啟用至少 2 個選項，在您的使用者重設其密碼時給予最大的彈性。<br><br></li>
                  <li class="unordered">
如果您使用安全性問題，我們強烈建議您使用它們搭配其他驗證方法，因為安全性問題比電話或電子郵件密碼重設方法較不安全。<br><br></li>
                </ul>
                <p>
                  <strong>使用哪些目錄欄位？</strong>
                </p>
                <ul>
                  <li class="unordered">
辦公室電話對應至目錄中使用者物件上的 [辦公室電話]<strong></strong> 屬性。<br><br></li>
                  <li class="unordered">
行動電話對應至目錄中使用者物件上的 [驗證行動]<strong></strong> 屬性 (不公開可見) 或 [行動電話]<strong></strong> 屬性 (公開可見)。  服務會先檢查 [驗證電話]<strong></strong> 的資料，如果沒有任何資料存在，就會回到 [行動電話]<strong></strong> 屬性。<br><br></li>
                  <li class="unordered">
備用電子郵件地址對應至目錄中使用者物件上的 [驗證電子郵件]<strong></strong> 屬性 (不公開可見) 或 [備用電子郵件]<strong></strong> 屬性 (公開可見)。  服務會先檢查 [驗證電子郵件]<strong></strong> 的資料，如果沒有任何資料存在，就會回到 [備用電子郵件]<strong></strong> 屬性。<br><br></li>
                  <li class="unordered">
安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。  基於安全性考量，目前系統管理員無法編輯或者查看這些答案。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>依預設，只有雲端屬性辦公室電話和行動電話會從您的內部部署目錄同步處理至您的雲端目錄。  若要深入了解哪些內部部署屬性會同步處理至雲端，請參閱<a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">屬性同步處理至 Azure AD</a>。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
會影響當使用者註冊時，所顯示的驗證方法。  如果您未啟用指定的驗證方法，使用者將無法自行註冊該驗證方法。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者目前無法註冊自己的辦公室電話號碼；必須由系統管理員定義該驗證方法。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定使用者可以針對指定驗證步驟使用哪些驗證方法做為挑戰。  例如，如果使用者在 Azure Active Directory 中的 [辦公室電話]<strong></strong> 和 [驗證電話]<strong></strong> 欄位都具有資料，則他或她可以使用其中一種驗證方法來復原其密碼。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者只有當您已啟用為系統管理員的驗證方法中有資料存在時，才可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>必要驗證方法數目</p>
                </div>
              </td>
              <td>
                <p>判斷使用者必須通過才能重設其密碼的可用驗證方法數目下限。</p>
                <p>(只有在 [使用者啟用密碼重設]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
可以設為 1 或 2 個必要驗證方法。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定使用者在完成註冊體驗之前，必須註冊的驗證方法數目下限。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
會影響使用者能夠重設密碼之前，必須通過的驗證步驟數目。  驗證步驟是定義為使用者使用一段驗證資訊 (例如撥打其辦公室電話，或是傳送至備用電子郵件的電子郵件) 以驗證其帳戶。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>如果使用者在他或她的帳戶上沒有根據您已設定之原則才能成功重設其密碼進行定義的必要數目驗證資訊，則使用者會看到錯誤頁面，引導他們要求系統管理員重設其密碼。  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>註冊所需的問題數目</p>
                </div>
              </td>
              <td>
                <p>決定當註冊安全性問題選項時，使用者必須回答的問題數目下限。</p>
                <p>(只有在已啟用 [安全性問題]<strong></strong> 核取方塊時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
可以設定為 3 – 5 個註冊所需的問題。<br><br></li>
                  <li class="unordered">
註冊所需的問題數目必須大於或等於重設所需的問題數目。<br><br></li>
                  <li class="unordered">
我們建議您將註冊所需的問題數目設定為大於重設所需的問題數目，讓使用者在重設其密碼時享有更大的彈性。  因為我們會從已註冊的所有問題的集區中隨機選取問題讓使用者回答，所以這是更安全的組態。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定使用者被視為完全註冊密碼重設之前，使用者必須回答的問題數目下限。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>重設所需的問題數目 </p>
                </div>
              </td>
              <td>
                <p>決定當重設密碼時，使用者必須回答的問題數目下限。</p>
                <p>(只有在已啟用 [安全性問題]<strong></strong> 核取方塊時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
可以設定為 3 – 5 個重設所需的問題。<br><br></li>
                  <li class="unordered">
重設所需的問題數目必須小於或等於註冊所需的問題數目。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定在使用者的密碼可以重設之前，使用者必須回答的問題數目下限。<br><br></li>
                  <li class="unordered">
重設密碼時，會從使用者已註冊問題總清單中，隨機選取這個數目的問題。  例如，如果使用者有 5 個已註冊的問題，當使用者重設密碼時，會從其中隨機選取 3 個問題。  使用者必須正確回答所有問題，才能重設密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>以知識為基礎的安全性問題</p>
                </div>
              </td>
              <td>
                <p>定義您的使用者在註冊密碼重設以及重設其密碼時，可以選取的預先定義安全性問題。</p>
                <p>(只有在已啟用 [安全性問題]<strong></strong> 核取方塊時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
所有以知識為基礎的問題將會依據使用者的瀏覽器地區設定，當地語系化成完整的 O365 語言集。<br><br></li>
                  <li class="unordered">
最多總共可以定義 20 個問題 (您的自訂與以知識為基礎的安全性問題總和)。<br><br></li>
                 <li class="unordered">
答案字元最小值限制為 3 個字元。<br><br></li>
                  <li class="unordered">
答案字元最大值限制為 40 個字元。<br><br></li>
                  <li class="unordered">
使用者不能回答相同的問題兩次。<br><br></li>
                  <li class="unordered">
使用者不能對兩個不同的問題提供相同的答案兩次。<br><br></li>
                  <li class="unordered">
可以使用任何字元集來定義解答 (包括 Unicode 字元)。<br><br></li>
                  <li class="unordered">
定義的的問題數目必須大於或等於註冊所需的問題數目。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定當註冊密碼重設時，使用者可以對哪些問題提供答案。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定使用者可以使用哪些問題來重設密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>自訂安全性問題</p>
                </div>
              </td>
              <td>
                <p>定義您的使用者在註冊密碼重設以及重設其密碼時，可能會選取的安全性問題。</p>
                <p>(只有在已啟用 [安全性問題]<strong></strong> 核取方塊時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
最多總共可以定義 20 個問題 (您的自訂與以知識為基礎的安全性問題總和)。<br><br></li>
                  <li class="unordered">
問題字元最大值限制為 200 個字元。<br><br></li>
                  <li class="unordered">
答案字元最小值限制為 3 個字元。<br><br></li>
                  <li class="unordered">
答案字元最大值限制為 40 個字元。<br><br></li>
                  <li class="unordered">
使用者不能回答相同的問題兩次。<br><br></li>
                  <li class="unordered">
使用者不能對兩個不同的問題提供相同的答案兩次。<br><br></li>
                  <li class="unordered">
可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。<br><br></li>
                  <li class="unordered">
定義的的問題數目必須大於或等於註冊所需的問題數目。<br><br></li>
                  <li class="unordered">
自訂問題不支援針對不同的地區設定定義不同的問題。  所有自訂問題將會以您在系統管理 UI 中用來輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。  如果您需要當地語系化這些問題，請改為使用 「以知識為基礎」的問題。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定當註冊密碼重設時，使用者可以對哪些問題提供答案。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
決定使用者可以使用哪些問題來重設密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>登入時要求使用者註冊？</p>
                </div>
              </td>
              <td>
                <p>決定使用者下一次登入時是否需要註冊連絡資料以進行密碼重設。  
                </p>
                <p>這項功能適用於任何使用工作或學校帳戶的登入頁面。  這類頁面包括所有 Office 365、Azure 管理入口網站、存取面板和任何使用 Azure AD 來登入的同盟或自訂開發應用程式。
                </p>
                <p>強制執行的註冊將僅適用於啟用密碼重設的使用者，因此，如果您已經使用「限制密碼重設的存取」功能並將密碼重設的範圍限定為一組特定的使用者，則只有該群組中的使用者需於登入時註冊密碼重設。</p>
                <p>(只有在 [使用者啟用密碼重設]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果您停用這項功能，您也可以手動將使用者傳送至 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> 以註冊其連絡資料。  <br><br></li>
                  <li class="unordered">
使用者也可以藉由按一下存取面板中設定檔索引標籤底下的 [註冊密碼重設]<strong></strong> 連結，連接到註冊入口網站。<br><br></li>
                  <li class="unordered">
透過這種方式註冊可以藉由按一下 [取消] 按鈕或關閉視窗來關閉，但是如果使用者未註冊，則每次登入時都會被叨擾。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
此設定不會影響註冊入口網站本身的行為，而是會決定當使用者登入存取面板時是否對其顯示註冊入口網站。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>使用者必須確認其連絡資料的天數</p>
                </div>
              </td>
              <td>
                <p>當 [要求使用者註冊]<strong></strong> 開啟時，此設定會決定使用者必須重新確認其資料之前所經過的時間長度。 </p>
                <p>(只有在 [當登入存取面板時要求使用者註冊]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
接受介於 0-730 天的值，0 天表示永遠不會要求使用者重新確認其連絡資料。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
此設定不會影響註冊入口網站本身的行為，而是會決定當使用者的連絡資料需要確認時是否對其顯示註冊入口網站。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>自訂連絡您的系統管理員連結？</p>
                </div>
              </td>
              <td>
                <p>控制當發生錯誤或使用者於作業等候太久時，在密碼重設入口網站上顯示的連絡您的系統管理員連結 (顯示在左側) 是否指向自訂 URL 或電子郵件地址。</p>
                <p>(只有在 [使用者啟用密碼重設]<strong></strong> 設為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果您啟用此設定，您必須選擇自訂 URL 或電子郵件地址，方法是填寫緊接在此設定下方的 [自訂電子郵件地址或 URL]<strong></strong> 欄位。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，使用者按一下反白顯示的連結會發送電子郵件給要求重設其密碼之所有租用戶系統管理員的主要電子郵件地址。  這封電子郵件是以下列邏輯發送：<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
如果有密碼管理員，則傳送電子郵件給所有密碼管理員，總計最多 100 個收件者。<br><br></li>
                      <li class="unordered">
如果沒有密碼管理員，則傳送電子郵件給所有使用者系統管理員，總計最多 100 個收件者。<br><br></li>
                      <li class="unordered">
如果沒有使用者系統管理員，則傳送電子郵件給所有全域管理員，總計最多 100 個收件者。<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
如果設定為是，此設定會自訂上述反白顯示連結的行為，以指向自訂 URL 或電子郵件地址，您的使用者可以瀏覽以取得密碼重設的協助。<br><br></li>
                  <li class="unordered">
如果您指定 URL，它會在新索引標籤中開啟。<br><br></li>
                  <li class="unordered">
如果您指定電子郵件地址，我們會建立該電子郵件地址的 mailto 連結。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>自訂電子郵件地址或 URL</p>
                </div>
              </td>
              <td>
                <p>控制 [連絡您的系統管理員]<strong></strong> 連結所指向的電子郵件地址或 URL。 </p>
                <p>(只有在 [自訂連絡您的系統管理員連結]<strong></strong> 設定為 [是]<strong></strong> 時才能看見)。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
必須是有效的內部網路或外部網路 URL 或電子郵件地址。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
變更 [連絡您的系統管理員]<strong></strong> 連結指向的位置。<br><br></li>
                  <li class="unordered">
如果您提供電子郵件地址，連結會變成該電子郵件地址的 “mailto” 連結。<br><br></li>
                  <li class="unordered">
如果您提供 URL，連結會變成指向該 URL 的標準 href，在新索引標籤中開啟。  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>將密碼回寫至內部部署目錄</p>
                </div>
              </td>
              <td>
                <p>控制是否對此目錄啟用密碼回寫，如果回寫開啟，則表示內部部署回寫服務的狀態。</p>
                <p>如果您想要暫時停用服務而不重新設定 Azure AD Connect，此設定就很有用。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
只有在您已藉由下載最新版本的 Azure AD Connect 並且在 [選用功能]<strong></strong> 選取畫面底下啟用 [密碼回寫]<strong></strong> 以安裝密碼回寫時，此控制項才會顯示。<br><br></li>
                  <li class="unordered">
如果您已啟用密碼回寫，而且覺得服務有組態問題，您可以前往這個索引標籤，並查看 [密碼寫回服務狀態]<strong></strong> 標籤以查看是否有錯誤。<br><br></li>
                  <li class="unordered">
可以顯示的狀態如下：<br><br><ul><li class="unordered"><strong>已設定</strong> - 一切如預期般運作<br><br></li><li class="unordered"><strong>未設定</strong> - 您已安裝回寫，但我們無法取用服務，請檢查以確定您並未封鎖與 443 的輸出連線，如果仍有問題，嘗試重新安裝服務。<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果回寫已部署及設定，且此參數設定為<strong>否</strong>，則回寫會停用，而且同盟和密碼雜湊同步處理使用者將無法註冊密碼重設，以重設其密碼。<br><br></li>
                  <li class="unordered">
如果此參數設定為 [是]<strong></strong>，則回寫會啟用，而且同盟和密碼雜湊同步處理使用者可以重設其密碼。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果回寫已部署及設定，且此參數設定為<strong>否</strong>，則回寫會停用，而且同盟和密碼雜湊同步處理使用者將無法重設其密碼。<br><br></li>
                  <li class="unordered">
如果此參數設定為 [是]<strong></strong>，則回寫會啟用，而且同盟和密碼雜湊同步處理使用者可以重設其密碼。<br><br></li>
                </ul>
              </td
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>讓使用者直接解除鎖定內部部署 Active Directory 帳戶，而不必重設其密碼</p>
                </div>
              </td>
              <td>
              <p>指定是否應為瀏覽密碼重設入口網站的使用者提供選項，讓他們在不重設密碼的情況下解除鎖定內部部署的 Active Directory 帳戶。 根據預設，Azure AD 執行密碼重設時會一律解除鎖定帳戶，此設定可讓您區隔這兩項作業。</p>
              <p>如果設為「是」，會提供使用者重設其密碼以解除鎖定帳戶的選項，或是在不重設密碼的情況下解除鎖定的選項。 </p>
              <p>如果設定為「否」，使用者將只能執行合併的密碼重設和帳戶解除鎖定作業。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
若要使用這項功能，您必須安裝 2015 年 8 月或更新版本的 Azure AD Connect (v. 1.0.8667.0 或更新版本)。<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">按一下這裡以下載最新版本的 Azure AD Connect</a>。</li>
                  <li class="unordered">
                    <strong>附註：</strong>為測試這項功能，您需要啟用密碼回寫，並使用來自內部部署 (例如同盟或密碼同步處理使用者) 的帳戶，而且有一個已鎖定的帳戶。  非內部部署而且沒有鎖定帳戶的使用者，將看不到解除鎖定其帳戶的選項。</li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
啟用這個選項後，當內部部署帳戶被鎖定的使用者抵達密碼重設入口網站時，將會提供他或她在不重設密碼的情況下解除鎖定其帳戶的選項。<br><br>請注意，如果您使用密碼回寫，密碼重設時帳戶就已自動解除鎖定，這個選項只是要區隔這些作業。<br><br>如果您發現您的許多服務台個案都是因為帳戶解除鎖定要求所產生，則啟用這個選項特別實用。</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-notifications"></a>密碼管理通知
下表描述每個控制項如何影響接收密碼重設通知之使用者和系統管理員的體驗。  您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [設定] 索引標籤的 [通知] 區段底下，設定這些選項。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>當其他系統管理員重設自己的密碼時通知系統管理員</p>
                </div>
              </td>
              <td>
                <p>決定當任何類型的其他系統管理員重設自己的密碼時，是否透過傳送至其主要電子郵件地址的電子郵件，通知所有全域管理員。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，則不會傳送通知。<br><br></li>
                  <li class="unordered">
如果設定為是，則當任何單一系統管理員重設自己的密碼時，會通知所有全域管理員。<br><br></li>
                  <li class="unordered">
此通知是透過傳送至組織中所有其他全域管理員主要電子郵件地址的電子郵件來傳送的。<br><br></li>
                </ul>
                <p>
                  <strong>範例：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果已啟用此選項，當系統管理員 A 重設其密碼，且租用戶中有其他 3 個系統管理員 B、C 和 D，則系統管理員 B、C 和 D 會收到一封電子郵件，表示系統管理員 A 重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>當使用者和系統管理員的密碼被重設時通知他們</p>
                </div>
              </td>
              <td>
                <p>決定重設自己密碼的使用者或系統管理員是否會收到電子郵件通知，表示他們的密碼已重設。</p>
                <br>
                <p><b><u>需要下列其中一個授權 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">深入了解</a></u></b></p>
                 <ul>
                   <li>O365 (任何付費 SKU) [僅限雲端使用者]</li>
                   <li>Azure AD Basic [僅限雲端使用者]</li>
                   <li>Azure AD Premium P1 或 P2 [雲端和內部部署使用者]</li>
                   <li>Enterprise Mobility Suite [雲端和內部部署使用者]</li>
                   <li>Enterprise Cloud Suite [雲端和內部部署使用者]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
如果設定為否，則不會傳送通知。<br><br></li>
                  <li class="unordered">
如果設定為是，則每當使用者或系統管理員重設自己的密碼時，他或她會收到通知，指出他或她的密碼已重設。<br><br></li>
                  <li class="unordered">
此通知會透過電子郵件傳送給重設其密碼之使用者的「使用者主體名稱」及備用 (或驗證) 電子郵件地址。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## <a name="next-steps"></a>後續步驟
以下是所有 Azure AD 密碼重設文件頁面的連結：

* **您來到此處是因為有登入問題嗎？** 若是如此， [以下是如何變更和重設密碼的說明](active-directory-passwords-update-your-own-password.md#reset-your-password)。
* [**運作方式**](active-directory-passwords-how-it-works.md) - 了解六個不同的服務元件及其功能
* [**開始使用**](active-directory-passwords-getting-started.md) - 了解如何讓使用者重設及變更雲端或內部部署密碼
* [**最佳作法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理組織的密碼
* [**深入探索**](active-directory-passwords-get-insights.md) - 了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) - 取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何快速移難排解服務的問題
* [**深入了解**](active-directory-passwords-learn-more.md) - 深入探索服務運作方式的技術細節

[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"

