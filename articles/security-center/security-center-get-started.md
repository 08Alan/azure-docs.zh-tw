<properties
   pageTitle="Azure 資訊安全中心快速入門指南 | Microsoft Azure"
   description="本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="terrylan"/>

# Azure 資訊安全中心快速入門指南

本文帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。

> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。本文件將使用範例部署來介紹服務。這不是逐步指南。

## 什麼是 Azure 資訊安全中心？
資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。它可在您的各個訂用帳戶之間提供整合式安全性監視和原則管理、協助偵測可能被忽略的威脅，並且適用於廣泛的安全性解決方案生態系統。

## 資料收集

資訊安全中心會收集虛擬機器的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。建議啟用資料收集，但您可以在資訊安全中心原則中關閉資料收集來選擇退出。下列步驟說明如何關閉資料收集。

## 必要條件

若要開始使用資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。資訊安全中心已經由訂用帳戶啟用。如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。

資訊安全中心是從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取。如需詳細資訊，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。


## 存取資訊安全中心

在入口網站中，執行下列步驟來存取資訊安全中心：

1. 選取 [瀏覽] 然後捲動至 [資訊安全中心] 選項。![在入口網站存取 Azure 資訊安全中心][1]

2. 選取 [資訊安全中心]。隨即開啟 [資訊安全中心] 刀鋒視窗。
3. 為方便日後快速存取 [資訊安全中心] 刀鋒視窗，請選取 [將刀鋒視窗釘選到儀表板] 選項 (右上方)。![將刀鋒視窗釘選到儀表板選項][2]

## 使用資訊安全中心

您可以設定 Azure 訂用帳戶和資源群組的安全性原則。一同設定您訂用帳戶的安全性**原則**：

1. 選取 [資訊安全中心] 刀鋒視窗上的 [原則] 圖格。![資訊安全中心][3]

2. 在 [安全性原則 – 定義每個訂用帳戶或資源群組的原則] 刀鋒視窗中，選取訂用帳戶。![Azure 資訊安全中心的 [安全性原則] 刀鋒視窗][4]

3. 在 [安全性原則] 刀鋒視窗中，會啟用 [資料收集] 以自動收集記錄檔。監視擴充功能會佈建在訂用帳戶的所有目前 VM 和新 VM 上。(您可以將 [資料收集] 設定為 [關閉]，選擇退出資料收集，但這會讓資訊安全中心無法為您提供安全性警示與建議。)
4. 選取 [選擇每個區域的儲存體帳戶]。針對每個有虛擬機器在其中執行的區域，您需選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。如不為每個區域選擇儲存體帳戶，系統會為您建立。基於安全性考量，收集到的資料在邏輯上會與其他客戶的資料隔離。

     > [AZURE.NOTE] 建議您啟用資料收集，並先在訂用帳戶層級選擇儲存體帳戶。安全性原則可以在 Azure 訂用帳戶層級和資源群組層級進行設定，但資料收集和儲存體帳戶只可在訂用帳戶層級進行設定。

5. 開啟想要查看的 [建議]，當做安全性原則的一部分。範例：

 - 開啟 [系統更新] 會掃描所有支援的虛擬機器，尋找遺漏的 OS 更新。
 - 開啟 [基準規則] 會掃描所有支援的虛擬機器，識別任何可能讓虛擬機器更容易受到攻擊的 OS 設定。

處理**建議**：

1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [建議] 圖格。資訊安全中心會定期分析 Azure 資源的安全性狀態。發現潛在的安全性弱點時，會在這裡顯示建議。
2.	選取每個建議，檢視詳細資訊及/或採取行動以解決問題。![Azure 資訊安全中心的建議][5]

透過 [資源安全性健康狀態] 檢視資源的健康狀態及安全性狀態：

1.	返回 [資訊安全中心] 刀鋒視窗。
2.	[資源安全性健康狀態] 圖格包含**虛擬機器**、**網路**、**SQL** 和**應用程式**的安全性狀態指標。
3.	請選取 [虛擬機器] 檢視詳細資訊。
4.	[虛擬機器] 刀鋒視窗會顯示狀態摘要，其中顯示反惡意程式碼程式、系統更新與重新啟動的狀態，以及虛擬機器的基準規則。
5.	在 [虛擬機器建議] 下選取項目，檢視詳細資訊及/或採取行動以設定必要的控制項。
6.	向下切入以檢視特定虛擬機器的其他資訊。![Azure 資訊安全中心的 [資源健康狀態] 磚][6]

處理**安全性警示**：

1.	返回 [資訊安全中心] 刀鋒視窗，然後選取 [安全性警示] 圖格。[安全性警示] 刀鋒視窗中會顯示警示清單。資訊安全中心透過對安全性記錄檔和網路活動的分析，產生警示。也包含來自整合式合作夥伴解決方案的警示。![Azure 資訊安全中心的安全性警示][7]

2.	選取警示以檢視其他資訊。![Azure 資訊安全中心的安全性警示詳細資料][8]

檢視 [合作夥伴解決方案] 的健康狀態：

1. 返回 [資訊安全中心] 刀鋒視窗。[合作夥伴解決方案] 圖格可讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。
2. 選取 [合作夥伴解決方案] 圖格。隨即開啟一個刀鋒視窗，其中顯示已連接到資訊安全中心的合作夥伴解決方案清單。![合作夥伴解決方案][9]

3. 選取合作夥伴解決方案。在此範例中，選取 **F5 WAF2** 解決方案。隨即開啟一個刀鋒視窗，其中顯示合作夥伴解決方案的狀態和解決方案相關聯的資源。選取 [解決方案主控台] 以開啟此解決方案的合作夥伴管理體驗。![合作夥伴解決方案詳細資料][10]

## 後續步驟
本文介紹了資訊安全中心的安全性監視和原則管理元件。若要深入了解，請參閱下列文章：

- [在 Azure 資訊安全中心內設定安全性原則](security-center-policies.md)--了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)--了解建議如何協助您保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
- [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)--了解如何管理與回應安全性警示。
- [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)--了解如何監視合作夥伴解決方案的健全狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)--尋找使用服務的常見問題。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)--取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png

<!---HONumber=AcomDC_0615_2016-->