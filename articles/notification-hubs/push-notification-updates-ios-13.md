---
title: Uppdateringar för Azure Notification Hubs iOS 13 | Microsoft Docs
description: Läs om iOS 13-avbryter ändringar i Azure Notification Hubs
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228146"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs-uppdateringar för iOS 13

Apple har nyligen gjort några ändringar i sin offentliga push-tjänst. ändringarna är mest justerade med versionerna av iOS 13 och Xcode. I den här artikeln beskrivs effekterna av de här ändringarna på Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>Ändringar av APNS-push-nyttolast

### <a name="apns-push-type"></a>APNS-push-typ

Apple kräver nu att utvecklare identifierar meddelanden som aviserings-eller bakgrunds meddelanden via det nya `apns-push-type`s huvudet i APN-API: et. Enligt [Apples dokumentation](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "värdet för den här rubriken måste återge innehållet i meddelandets nytto Last korrekt. Om det uppstår någon felmatchning eller om rubriken saknas på system där den krävs kan APN returnera ett fel, fördröja leveransen av meddelandet eller ta bort alltihop.”

Utvecklare måste nu ange rubriken i program som skickar meddelanden via Azure Notification Hubs. På grund av en teknisk begränsning måste kunderna använda tokenbaserad autentisering för autentiseringsuppgifter för APN med begär Anden som inkluderar det här attributet. Om du använder certifikatbaserad autentisering för dina APN-autentiseringsuppgifter måste du växla till med hjälp av tokenbaserad autentisering.

Följande kod exempel visar hur du ställer in detta huvud-attribut i aviserings begär Anden som skickas via Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Mal meddelanden – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Inbyggda meddelanden – .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Direkta REST-anrop

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

För att hjälpa dig under den här över gången, när Azure Notification Hubs identifierar ett meddelande som inte har `apns-push-type` har angetts, härleds push-typen från meddelande förfrågan och värdet anges automatiskt. Kom ihåg att du måste konfigurera Azure-Notification Hubs för att använda tokenbaserad autentisering för att ange det obligatoriska huvudet. Mer information finns i [token-baserad (http/2)-autentisering för APN](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>APN-prioritet

En annan mindre förändring, men en som kräver en ändring i Server dels programmet som skickar meddelanden, är kravet på att `apns-priority` rubriken måste anges till 5 för bakgrunds meddelanden. Många program ställer in `apns-priority` rubriken på 10 (indikerar omedelbar leverans) eller anger inte det och hämtar standardvärdet (som också är 10).

Att ställa in det här värdet på 10 tillåts inte längre för bakgrunds meddelanden och du måste ange värdet för varje begäran. Apple levererar inte bakgrunds meddelanden om detta värde saknas. Exempel:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK-ändringar

För år använde iOS-utvecklare `description`-attributet för de `deviceToken` data som skickats till Sänd-token delegera för att extrahera den push-token som ett Server dels program använder för att skicka meddelanden till enheten. Med Xcode 11 `description` attributet ändrat till ett annat format. Befintlig kod som utvecklare använde för det här attributet är nu bruten. Vi har uppdaterat Azure Notification Hubs SDK för att hantera den här ändringen, så uppdatera SDK som används av dina program till version 2.0.4 eller senare av [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
