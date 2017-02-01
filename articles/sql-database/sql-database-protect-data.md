---
title: "Azure SQL Database 資料保護概觀 | Microsoft Docs"
description: "了解如何保護 Azure SQL Database 中的資料。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>保護 Azure SQL Database 中的資料

SQL Database 使用加密功能保護您的資料。  

## <a name="overview"></a>概觀

SQL Database 會使用[傳輸層安全性](https://support.microsoft.com/en-us/kb/3135244)為移動中的資料提供加密、使用[透明資料加密](http://go.microsoft.com/fwlink/?LinkId=526242)為待用資料提供加密，使用[一律加密](https://msdn.microsoft.com/library/mt163865.aspx)為使用中的資料提供加密，進而保護您的資料。 如需在 SQL Database 中使用這些資料保護功能的討論，請參閱[資料保護和安全性](sql-database-protect-data.md)。

如需其他的資料加密方式，請考慮：

* [儲存格層級加密](https://msdn.microsoft.com/library/ms179331.aspx) ，可利用不同的加密金鑰來加密特定的資料行或甚至是資料儲存格。
* 如果您需要硬體安全性模組或集中管理您的加密金鑰階層，請考慮 [在 Azure VM 中使用 Azure 金鑰保存庫搭配 SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。


SQL Database 可藉由提供稽核和威脅偵測功能來保護您的資料。 

### <a name="auditing"></a>稽核
SQL Database 稽核會將資料庫事件記錄到 Azure 儲存體帳戶中的稽核記錄檔，藉此追蹤資料庫活動並協助您維護法規相符性。 稽核可讓您了解進行中的資料庫活動，以及分析和調查歷史活動，以找出潛在威脅或可疑的濫用和安全性違規。 如需其他資訊，請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)。  

### <a name="threat-detection"></a>威脅偵測
威脅偵測會提供 Azure SQL Database 服務內建的額外安全情報層，以補充稽核的不足之處。 這項功能全天候運作，以了解、剖析及偵測異常資料庫活動。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以依照所提供的資訊式和可動作指示來回應警示。 如需詳細資訊，請參閱 [開始使用 SQL Database 威脅偵測](sql-database-threat-detection-get-started.md)。  

## <a name="connection-security"></a>連接安全性
「連線安全性」是指如何使用防火牆規則和連線加密，限制和保護資料庫的連線。

伺服器和資料庫兩者都可使用防火牆規則，拒絕來自未明確設為允許清單的 IP 位址的連線嘗試。 若要允許應用程式或用戶端機器的公用 IP 位址，嘗試連線至新的資料庫，您必須先使用 Azure 傳統入口網站、REST API 或 PowerShell 建立伺服器層級的防火牆規則。 最好的作法是，您應該盡可能限制允許穿透您伺服器防火牆的 IP 位址範圍。 如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/ee621782)。

Azure SQL Database 的所有連線，也就是任何時候只要資料需要「傳輸」進出資料庫時，都需要加密 (SSL/TLS)。 在您應用程式的連接字串中，您必須指定參數來加密連線，並且 *不要* 信任伺服器憑證 (這是為了如果您從 Azure 傳統入口網站將連接字串複製出去)，否則連線將無法驗證伺服器的身分識別，也可能會遭受到「攔截」攻擊。 例如對於 ADO.NET 驅動程式，這些連接字串參數是 **Encrypt=True** 和 **TrustServerCertificate=False**。 如需詳細資訊，請參閱 [Azure SQL Database 連線加密和憑證驗證](https://msdn.microsoft.com/library/azure/ff394108#encryption)。

## <a name="authentication"></a>驗證
「驗證」是指連線到資料庫時如何證明身分識別。 SQL Database 支援兩種驗證類型：

* **SQL 驗證**，其需要使用者名稱和密碼
* **Azure Active Directory 驗證**，它會使用由 Azure Active Directory 管理的身分識別，並支援受管理和整合的網域

當您為資料庫建立邏輯伺服器時，採取使用者名稱和密碼指定了「伺服器管理員」登入。 使用這些認證，您就可以使用資料庫擁有者或 "dbo" 的身分驗證該伺服器上的任何資料庫。 如果您想要使用 Azure Active Directory 驗證，就必須建立另一個名為「Azure AD 管理員」的伺服器管理員，其能夠管理 Azure AD 使用者和群組。 此管理員也可以執行一般伺服器管理員可執行的所有作業。 如需如何建立 Azure AD 管理員以啟用 Azure Active Directory 驗證的逐步解說，請參閱 [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md) 。

最好的做法是，您的應用程式應該使用不同的帳戶來驗證，因為萬一應用程式的程式碼容易受到 SQL 插入式攻擊，您就可以限制授與應用程式的權限，並降低惡意活動的風險。 建議的方法是建立[自主資料庫使用者](https://msdn.microsoft.com/library/ff929188)，讓您的應用程式直接與資料庫進行驗證。 您可以藉由執行下列 T-SQL 命令，在以伺服器管理員身分登入連線到您的使用者資料庫時，建立使用 SQL 驗證的自主資料庫使用者：

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

如果您建立了 Azure AD 管理員，就可以藉由執行下列 T-SQL 命令，在以 Azure AD 管理員身分登入連線到您的使用者資料庫時，建立使用 Azure Active Directory 驗證的自主資料庫使用者：

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

在這兩個案例中，應用程式的連接字串應該指定這些使用者認證 (而不是伺服器管理員登入)，以連線到資料庫。

如需驗證 SQL Database 的詳細資訊，請參閱 [在 Azure SQL Database 中管理資料庫和登入](sql-database-manage-logins.md)。

## <a name="authorization"></a>Authorization
「授權」是指在 Azure SQL 資料庫內可以執行的動作，這是由使用者帳戶的角色成員資格和權限控制。 最好的作法是，您應該授與使用者所需的最低權限。 Azure SQL Database 可以在 T-SQL 中很容易使用角色進行上述管理：

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

您所連線的伺服器管理員帳戶是 db_owner 的成員，有權限在資料庫中執行任何動作。 請儲存此帳戶，以便部署結構描述升級及其他管理作業。 請使用具更多有限權限的 "ApplicationUser" 帳戶，從應用程式連線到具應用程式所需之最低權限的資料庫。

有許多方式可以進一步限制使用者透過 Azure SQL Database 可以執行的動作：

* 除了 db_datareader 和 db_datawriter 以外，[資料庫角色](https://msdn.microsoft.com/library/ms189121)均可以用來建立權力較大的應用程式使用者帳戶，或權力較小的管理帳戶。
* 細微的 [權限](https://msdn.microsoft.com/library/ms191291) 可讓您控制您可以對資料庫中個別資料行、資料表、檢視、程序和其他物件執行哪些作業。
* [模擬](https://msdn.microsoft.com/library/vstudio/bb669087)和[模組簽署](https://msdn.microsoft.com/library/bb669102)可用來安全地暫時提升權限。
* [資料列層級安全性](https://msdn.microsoft.com/library/dn765131) 可用於使用者可存取資料列的限制。
* [資料遮罩](sql-database-dynamic-data-masking-get-started.md) 可用來限制公開機密資料。
* [預存程序](https://msdn.microsoft.com/library/ms190782) 可用來限制對資料庫可採取的動作。

要從 Azure 傳統入口網站或使用 Azure 資源管理員 API 管理資料庫和邏輯伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關此主題的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../active-directory/role-based-access-control-configure.md)。

## <a name="encryption"></a>加密
Azure SQL Database 可以使用 [透明資料加密](http://go.microsoft.com/fwlink/?LinkId=526242)，透過資料在「靜止」時或儲存在資料庫檔案和備份中時加密資料，來協助保護您的資料。 若要加密您的資料庫，請以資料庫擁有者的身分連線，然後執行：

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

如需其他加密機密資料的方式，請考慮：

* [儲存格層級加密](https://msdn.microsoft.com/library/ms179331.aspx) ，可利用不同的加密金鑰來加密特定的資料行或甚至是資料儲存格。
* 如果您需要硬體安全性模組或集中管理您的加密金鑰階層，請考慮 [在 Azure VM 中使用 Azure 金鑰保存庫搭配 SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。
* [一律加密](https://msdn.microsoft.com/library/mt163865.aspx) (預覽版) 讓加密對應用程式透明化，且允許用戶端加密用戶端應用程式中的機密資料，而不需與 SQL Database 共用加密金鑰。

## <a name="auditing"></a>稽核
稽核和追蹤資料庫事件可協助您遵循法規，並找出可疑的活動。 SQL Database 稽核可讓您將資料庫中的事件記錄到 Azure 儲存體帳戶中的稽核日誌。 SQL Database 稽核也整合了 Microsoft Power BI，具備向下鑽研報表和分析的功能。 如需詳細資訊，請參閱 [開始使用 Azure Database 稽核](sql-database-auditing-get-started.md)。

SQL Database 威脅偵測提供除了稽核之外另一層的安全性。 它可讓您在發生威脅時，藉由提供異常活動的安全性警示來回應威脅。 如需詳細資訊，請參閱 [開始使用 SQL Database 威脅偵測](sql-database-threat-detection-get-started.md)。  

## <a name="compliance"></a>法規遵循
除了上述可協助您的應用程式符合各種安全法規需求的特色和功能之外，Azure SQL Database 也定期參與稽核，並且經過認證符合許多法規標準。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，您可以在當中找到 [SQL Database 法規認證](https://azure.microsoft.com/support/trust-center/services/)的最新清單。


## <a name="next-steps"></a>後續步驟

- 如需所有 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。
- 如需在 SQL Database 中使用存取控制功能的討論，請參閱[控制存取](sql-database-control-access.md)。
- 如需主動式監視的討論，請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)和[開始使用 SQL Database 威脅偵測](sql-database-threat-detection-get-started.md)。



<!--HONumber=Dec16_HO4-->


