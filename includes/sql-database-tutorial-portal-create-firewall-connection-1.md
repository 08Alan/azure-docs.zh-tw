<a id="log-in-to-the-azure-portal" class="xliff"></a>

## 登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

## 使用 Azure 入口網站建立空白的 SQL Database

Azure SQL Database 會使用一組定義的[計算和儲存體資源](../articles/sql-database/sql-database-service-tiers.md)建立。 此資料庫建立於 [Azure 資源群組](../articles/azure-resource-manager/resource-group-overview.md)和 [Azure SQL Database 邏輯伺服器](../articles/sql-database/sql-database-features.md)內。 

遵循以下步驟來建立空白 SQL 資料庫。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 從 [新增] 頁面中選取 [資料庫]，然後從 [資料庫] 頁面中選取 [SQL Database]。 

   ![建立空白資料庫](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. 使用下列資訊填寫 SQL Database 表單，如上圖所示︰   

   | 設定 | 建議的值 | 說明 |
   | --------| --------------- | ----------- | 
   | **資料庫名稱** | mySampleDatabase | 如需有效的資料庫名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)。 | 
   | **訂用帳戶** | 您的訂用帳戶  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **選取來源** | 空白資料庫 | 指定應建立空白資料庫。 |
   ||||

4. 按一下 [伺服器] 為您的新資料庫建立及設定新的伺服器。 在**新伺服器表單**表單中填寫下列資訊︰ 

   | 設定 | 建議的值 | 說明 |
   | --------| --------------- | ----------- | 
   | **伺服器名稱** | 任何全域唯一名稱。 | 如需有效的伺服器名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 | 
   | **伺服器管理員登入** | 任何有效名稱。 | 如需有效的登入名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)。|
   | **密碼** | 任何有效密碼。 | 您的密碼至少要有 8 個字元，而且必須包含下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
   | **位置** | 任何有效位置。 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |
   ||||

   ![建立資料庫伺服器](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. 按一下 [選取] 。

6. 按一下 [定價層] 指定新資料庫的服務層和效能等級。 針對此快速入門，選取 [20 DTUs (20 個 DTU)] 和 [250] GB 儲存體。

   ![建立資料庫-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. 按一下 [Apply (套用)] 。  

8. 為空白資料庫選取**定序** (此教學課程中使用預設值)。 如需定序的詳細資訊，請參閱＜[定序](https://docs.microsoft.com/sql/t-sql/statements/collations)＞。

9. 按一下 [建立] 即可佈建資料庫。 佈建完成所需時間約 1.5 分。 

10. 在工具列上，按一下 [通知] 以監視部署程序。

   ![通知](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

## 使用 Azure 入口網站建立伺服器層級的防火牆規則

SQL Database 服務會在伺服器層級建立防火牆。 防火牆一開始會防止外部工具和應用程式連線到伺服器或伺服器上任何資料庫。 但建立可開啟特定 IP 位址的防火牆規則後，即可允許連線。 請遵循下列步驟，為您用戶端的 IP 位址建立 [SQL Database 伺服器層級防火牆規則](../articles/sql-database/sql-database-firewall-configure.md)，並讓外部連線僅能夠穿過您 IP 位址的 SQL Database 防火牆。 


> [!NOTE]
> Azure SQL Database 會透過連接埠 1433 通訊。 只有當網路防火牆允許通過連接埠 1433 的輸出流量時，您才可以與 SQL Database 連線。


1. 部署完成之後，按一下左側功能表中的 [SQL Database]，然後按一下 [SQL Database] 頁面上的 [mySampleDatabase]。 資料庫的 [概觀] 頁面隨即開啟，其中會顯示完整伺服器名稱 (例如 **mynewserver20170313.database.windows.net**)，並提供進一步的組態選項。 將這個完整伺服器名稱複製起來，以供稍後使用。

   > [!IMPORTANT]
   > 在後續的快速入門中，您需要此完整伺服器名稱才能連線到伺服器及其資料庫。
   > 

   ![伺服器名稱](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. 如先前映像所示，按一下工具列上的 [設定伺服器防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

   ![伺服器防火牆規則](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. 按一下工具列上的 [新增用戶端 IP]，將目前的 IP 位址新增至新的防火牆規則。 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

4. 按一下 [儲存] 。 系統便會為目前的 IP 位址建立伺服器層級防火牆規則，以便在邏輯伺服器上開啟連接埠 1433。

   ![設定伺服器防火牆規則](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. 依序按一下 [確定]，然後關閉 [防火牆設定] 頁面。

您現在可以使用 SQL Server Management Studio (SSMS) 等工具連線至 Azure SQL Database 伺服器和資料庫。 該連線是來自此 IP 位址，且使用先前建立的伺服器管理帳戶。


> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database 防火牆存取。 按一下此頁面上的 [關閉] 即可對所有 Azure 服務停用。


<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

## 使用 Azure 入口網站取得連接字串的值

在 Azure 入口網站中取得 Azure SQL Database 伺服器的完整伺服器名稱。 透過 SQL Server Management Studio，您可使用此完整伺服器名稱連接到您的伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 從左側功能表中選取 [SQL Database]，按一下 [SQL Database]頁面上您的資料庫。 

3. 在 Azure 入口網站中您資料庫的 [基本資訊] 窗格中，找到後複製 [伺服器名稱]。

   ![連線資訊](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
