---
title: "使用 Azure 通知中樞將推播通知傳送至 iOS | Microsoft Docs"
description: "在本教學課程中，您將了解如何使用 Azure 通知中樞，將推播通知傳送至 iOS 應用程式。"
services: notification-hubs
documentationcenter: ios
keywords: "推播通知,推播通知,ios 推播通知"
author: ysxu
manager: erikre
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 968e24b0441575be7ef17aac8ffaddb8fd16d3c6


---
# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>使用 Azure 通知中樞將推播通知傳送至 iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀
> [!NOTE]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。
> 
> 

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您將使用 [Apple Push Notification Service (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)，建立可接收推播通知的空白 iOS 應用程式。 

完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

## <a name="before-you-begin"></a>開始之前
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)上找到本教學課程的完整程式碼。 

## <a name="prerequisites"></a>必要條件
本教學課程需要下列各項：

* [行動服務 iOS SDK 1.2.4 版]
* 最新版的 [Xcode]
* 支援 iOS 8 (或更新版本) 的裝置
* [Apple Developer Program](https://developer.apple.com/programs/) 成員資格。
  
  > [!NOTE]
  > 基於推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。
  > 
  > 

完成本教學課程是參加 iOS app 所有其他通知中樞教學課程的先決條件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>針對 iOS 推播通知設定您的通知中樞
本節將引導您利用所建立的 **.p12** 推播憑證，建立新的通知中樞，並設定搭配 APNS 進行驗證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>按一下 [設定]<b></b> 刀鋒視窗中的 [通知服務]<b></b> 按鈕，然後選取 [Apple (APNS)]<b></b>。 按一下 [上傳憑證]<b></b>，然後選取您稍早匯出的 <b>.p12</b> 檔案。 確定您同時指定正確的密碼。</p>

<p>因為這是用於開發，請務必選取 [沙箱]<b></b> 模式。 只有在您想傳送推播通知給從市集購買 App 的使用者時，才使用 [生產]<b></b> 模式。</p>
</li>
</ol>
&emsp;&emsp;![在 Azure 入口網站中設定 APNS](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![在 Azure 入口網站中設定 APNS 憑證](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

現在已將您的通知中心設定成使用 APNS，而且您有可用來註冊應用程式和傳送推播通知的連接字串。

## <a name="connect-your-ios-app-to-notification-hubs"></a>將您的 iOS 應用程式連接到通知中樞
1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式]  範本。
   
       ![Xcode - Single View Application][8]
2. 設定新專案的選項時，請務必使用您先前在 Apple 開發人員入口網站上設定套件組合識別碼時使用的相同**產品名稱**和**組織識別碼**。
   
    ![Xcode - 專案選項][11]
3. 在 [目標] 下按一下您的專案名稱，然後按一下 [組建設定] 索引標籤並展開 [程式碼簽署身分識別]，接著在 [偵錯] 下設定您的程式碼簽署身分識別。 將 [層級] 從 [基本] 切換成 [全部]，然後將 [佈建設定檔] 設為您先前建立的佈建設定檔。
   
    如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下功能表列上的 Xcode，再依序按一下 [喜好設定]、[帳戶] 索引標籤、[檢視詳細資料] 按鈕、您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。
   
       ![Xcode - provisioning profile][9]
4. 下載 [行動服務 iOS SDK 1.2.4 版] ，然後將檔案解壓縮。 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 [新增檔案至] 選項，將 **WindowsAzureMessaging.framework** 資料夾新增至 Xcode 專案。 選取 [必要時複製項目]，然後按一下 [新增]。
   
   > [!NOTE]
   > 通知中樞 SDK 目前不支援 Xcode 7 上的 Bitcode。  您必須在專案的 [建置選項] 中將 [啟用 Bitcode] 設定為 [否]。
   > 
   > 
   
       ![Unzip Azure SDK][10]
5. 將新的標頭檔新增至名為 `HubInfo.h`的專案。 這個檔案會保留通知中心的常數。  新增下列定義，然後以您的「中樞名稱」以及先前記下的 *DefaultListenSharedAccessSignature* 取代字串常值預留位置。
   
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
6. 開啟 `AppDelegate.h` 檔案並新增下列 import 指示詞：
   
         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
7. 根據您的 iOS 版本，在 `AppDelegate.m file` 的 `didFinishLaunchingWithOptions` 方法中新增下列程式碼。 此程式碼會向 APN 註冊裝置控制代碼：
   
    對於 iOS 8：
   
         UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
   
    iOS 8 之前的版本：
   
         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
8. 在相同檔案中新增下列方法。 此程式碼會使用您在 HubInfo.h 中指定的連接資訊連接到通知中心。 然後，它可提供裝置權杖給通知中樞，讓通知中樞能夠傳送通知：
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }
   
        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
9. 如果應用程式在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert** ：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

1. 在裝置上建置並執行 app，以確認 app 能夠順利運作。

## <a name="send-test-push-notifications"></a>傳送測試推播通知
您可以在 [Azure 入口網站]中透過中樞刀鋒視窗中的 [疑難排解] 區段 (使用 [測試傳送] 選項) 傳送推播通知，以在您的應用程式中測試接收推播通知。

![Azure 入口網站 - 測試傳送][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-from-the-app"></a>(選擇性) 從應用程式傳送推播通知
> [!IMPORTANT]
> 此從用戶端應用程式傳送通知的範例僅供學習之用。 由於這需要 `DefaultFullSharedAccessSignature` 存在用戶端應用程式上，通知中樞可能的風險為使用者可以取得存取傳送未經授權的通知至您的用戶端。
> 
> 

如果您要從應用程式中傳送推播通知，本節提供使用 REST 介面執行此操作的範例。

1. 在 XCode 中，開啟 `Main.storyboard` 並從物件程式庫加入下列 UI 元件，以允許使用者在 app 中傳送推播通知。
   
   * 沒有標籤文字的標籤。 這將用來回報傳送通知時發生的錯誤。 **Lines** 屬性應該設為 **0**，如此才會自動根據左右邊界和檢視頂端的限制來調整大小。
   * 具有 [預留位置] 文字的文字欄位會設為 [輸入通知訊息]。 將欄位限制在標籤的下方，如下所示。 將檢視控制器設為輸出委派。
   * 標題為 **傳送通知** 的按鈕會限制在文字欄位的下方，並在水平置中的位置。
     
     檢視應如下所示：
     
     ![Xcode 設計工具][32]
2. 為與您的檢視相連接的標籤和文字欄位[新增輸出](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)，並更新您的 `interface` 定義，以支援 `UITextFieldDelegate` 和 `NSXMLParserDelegate`。 新增以下所示的三個屬性宣告，以協助呼叫 REST API 及剖析回應。
   
    您的 ViewController.h 檔案應該類似下列內容：
   
        #import <UIKit/UIKit.h>
   
        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }
   
        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;
   
        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;
   
        @end
3. 開啟 `HubInfo.h` 並新增下列常數，它們將用於傳送通知給您的中樞。 使用實際的 *DefaultFullSharedAccessSignature* 連接字串，取代預留位置字串常值。
   
        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
4. 將下列 `#import` 陳述式新增至 `ViewController.h` 檔案。
   
        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"
5. 在 `ViewController.m` 中，將下列程式碼新增至介面實作。 此程式碼將會剖析您的 *DefaultFullSharedAccessSignature* 連接字串。 如同 [REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx)中所述，這類已剖析的資訊將用來產生 **授權** 要求標頭的 SaS 權杖。
   
        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;
   
        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;
   
            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];
   
                @throw parseException;
            }
   
            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }
6. 在 `ViewController.m` 中，於檢視載入時更新 `viewDidLoad` 方法以剖析連接字串。 也將公用程式方法新增至介面實作，如下所示。  

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





1. 如 [REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx)所述，在 `ViewController.m` 中，將下列程式碼新增至介面實作，以產生將在 **Authorization** 標頭中提供的 SaS 授權權杖。
   
        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;
   
            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];
   
                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }
   
            return token;
        }
2. 按住 Ctrl 並從 [傳送通知] 按鈕拖曳至 `ViewController.m`，為 **Touch Down** 事件新增名為 **SendNotificationMessage** 的動作。 以下列程式碼更新方法，來使用 REST API 通知傳送。
   
        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }
   
        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];
   
            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];
   
            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];
   
            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                       [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
3. 在 `ViewController.m`中，加入下列委派方法，以支援關閉文字欄位的鍵盤。 按住 Ctrl 並從文字欄位拖曳至介面設計工具中的檢視控制器圖示，以將檢視控制器設為輸出委派。
   
        //===[ Implement UITextFieldDelegate methods ]===
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
4. 在 `ViewController.m` 中，加入下列委派方法，以支援使用 `NSXMLParser` 剖析回應。
   
       //===[ Implement NSXMLParserDelegate methods ]===
   
       -(void)parserDidStartDocument:(NSXMLParser *)parser
       {
           self.statusResult = @"";
       }
   
       -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
           namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
           attributes:(NSDictionary *)attributeDict
       {
           NSString * element = [elementName lowercaseString];
           NSLog(@"*** New element parsed : %@ ***",element);
   
           if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
           {
               self.currentElement = element;
           }
       }
   
       -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
       {
           self.statusResult = [self.statusResult stringByAppendingString:
               [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
       }
   
       -(void)parserDidEndDocument:(NSXMLParser *)parser
       {
           // Set the status label text on the UI thread
           dispatch_async(dispatch_get_main_queue(),
           ^{
               [self.sendResults setText:self.statusResult];
           });
       }
5. 建置專案並確認一切正確無誤。

> [!NOTE]
> 如果您在 Xcode7 中遇到有關 Bitcode 支援的建置錯誤，您應該在 Xcode 中將 [建置設定] > [啟用 Bitcode] \(ENABLE_BITCODE) 變更為 [否]。 通知中樞 SDK 目前不支援 Bitcode。 
> 
> 

您可以在 Apple [本機和推播通知程式設計指南]中找到所有可能的通知承載。

## <a name="checking-if-your-app-can-receive-push-notifications"></a>檢查您的應用程式是否可接收推播通知
若要在 iOS 上測試推播通知，您必須將應用程式部署至實體 iOS 裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式並確認註冊成功，然後按下 [確定] 。
   
    ![iOS 應用程式推播通知註冊測試][33]
2. 如上所述，您可以從 [Azure 入口網站]傳送測試推播通知。 如果您已在應用程式中加入傳送推播通知的程式碼，在文字欄位中觸控以輸入通知訊息。 接著，按下鍵盤上的 [傳送] 按鈕或檢視中的 [傳送通知] 按鈕，以傳送通知訊息。
   
    ![iOS 應用程式推播通知傳送測試][34]
3. 推播通知會從特定通知中樞傳送至所有已註冊要接收通知的所有裝置。
   
    ![iOS 應用程式推播通知接收測試][35]

## <a name="next-steps"></a>後續步驟
在此簡單範例中，您會將推播通知廣播到您已註冊的所有 iOS 裝置。 我們建議您繼續進行 [Azure 通知中樞透過 .NET 後端通知 iOS 使用者] 教學課程作為學習的下一步，該教學課程將逐步引導您使用標記，建立後端以傳送推播通知。 

如果您想要按興趣群組分隔使用者，您可以額外移至 [使用通知中樞傳送即時新聞] 教學課程。 

如需有關通知中樞的一般資訊，請參閱 [通知中樞指引]。

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[行動服務 iOS SDK 1.2.4 版]: http://aka.ms/kymw2g
[行動服務 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[開始使用行動服務]: /develop/mobile/tutorials/get-started-ios
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 佈建入口網站]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[開始在行動服務中使用推播通知]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure 通知中樞透過 .NET 後端通知 iOS 使用者]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[使用通知中樞傳送即時新聞]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure 入口網站]: https://portal.azure.com



<!--HONumber=Nov16_HO2-->


