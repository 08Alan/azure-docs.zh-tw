---
title: Azure Event Grid 概念
description: 說明 Azure Event Grid 與其概念。 定義 Event Grid 的數個重要元件。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/04/2018
ms.author: babanisa
ms.openlocfilehash: e55127e60470f8f95235893a14113b80e8d6565b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid 中的概念

Azure Event Grid 中的主要概念如下：

## <a name="events"></a>活動

事件是完整說明系統中發生內容的最小量資訊。  每個事件都有一般資訊，例如：事件來源、事件發生的時間，以及唯一識別碼。  每個事件也有只與特定事件類型相關的特定資訊。 例如，關於在 Azure 儲存體中建立新檔案的事件包含檔案相關詳細資料，例如 `lastTimeModified` 值。 或者，關於虛擬機器重新開機的事件包含虛擬機器的名稱，以及重新開機的原因。 每個事件會限制為 64 KB 資料。

## <a name="event-sourcespublishers"></a>事件來源/發行者

事件來源是事件發生的地點。 例如，Azure 儲存體是 Blob 建立事件的事件來源。 Azure VM 網狀架構是虛擬機器事件的事件來源。 事件來源負責將事件發佈至 Event Grid。

## <a name="topics"></a>主題

發行者將事件分類成各主題。 本主題包含發行者傳送事件的端點。 若要回應某些類型的事件，訂閱者會決定要訂閱的主題。 主題也提供事件結構描述，以便訂閱者可以了解如何適當地使用事件。

系統主題是由 Azure 服務提供的內建主題。 自訂主題是應用程式和協力廠商的主題。

在設計您的應用程式時，請針對每一個類別的相關事件建立自訂主題。 例如，請考慮使用應用程式來傳送與修改使用者帳戶和處理訂單有關的事件。 任何事件處理常式不太需要兩種類別的事件。 建立兩個自訂主題，並讓事件處理常式訂閱其感興趣的自訂主題。 訂閱自訂主題時，事件處理常式可依事件類型進行篩選。

## <a name="event-subscriptions"></a>事件訂閱

訂用帳戶指示 Event Grid 訂閱者有興趣接收主題上的何種事件。 訂用帳戶也掌握事件應如何傳遞至訂閱者的相關資訊。

## <a name="event-handlers"></a>事件處理常式

從 Event Grid 的觀點而言，事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。  Event Grid 支援多個訂閱者類型。 視訂閱者類型而定，Event Grid 依照不同的機制來保證事件的傳遞。  對於 HTTP Webhook 事件處理常式，事件會重試到處理常式傳回 `200 – OK` 的狀態碼為止。 對於 Azure 儲存體佇列。事件會重試到佇列服務可成功處理訊息推送至佇列為止。

## <a name="filters"></a>篩選器

訂閱主題時，您可以篩選傳送至端點的事件。 您可以依事件類型或主旨模式進行篩選。 如需詳細資訊，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。

## <a name="security"></a>安全性

Event Grid 提供訂閱主題和發佈主題的安全性。 訂閱時，您必須在資源或主題上具有適當權限。 發佈時，您必須具有該主題的 SAS 權杖或金鑰驗證。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

## <a name="failed-delivery"></a>傳遞失敗

當 Event Grid 無法確認訂閱者端點收到的事件時，它會重新傳遞事件。 如需詳細資訊，請參閱 [Event Grid 訊息傳遞與重試](delivery-and-retry.md)。

## <a name="next-steps"></a>後續步驟

* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
