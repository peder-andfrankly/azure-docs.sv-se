---
title: Konfigurera nyckelring
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar nyckel ringen så att din app kan cachelagra tokens i nyckel ringen.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b4599549e15d6ebe4d0bd04f96c89df86b0c0cd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917513"
---
# <a name="configure-keychain"></a>Konfigurera nyckelring

När [Microsoft Authentication Library för iOS och MacOS](msal-overview.md) (MSAL) loggar in en användare eller uppdaterar en token försöker det att cachelagra tokens i nyckel ringen. Med cachelagring av tokens i nyckel ringen kan MSAL tillhandahålla tyst enkel inloggning (SSO) mellan flera appar som distribueras av samma Apple-utvecklare. SSO uppnås via funktionen åtkomst grupper för nyckel ringar. Mer information finns i dokumentationen till Apples [nyckel rings objekt](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc).

Den här artikeln beskriver hur du konfigurerar app-rättigheter så att MSAL kan skriva cachelagrade tokens till iOS-och macOS-nyckel.

## <a name="default-keychain-access-group"></a>Standard åtkomst grupp för nyckel ringar

### <a name="ios"></a>iOS

MSAL på iOS använder `com.microsoft.adalcache` åtkomst grupp som standard. Det här är den delade åtkomst gruppen som används av både MSAL-och ADAL-SDK: er (Azure AD Authentication Library) och säkerställer den bästa funktionen för enkel inloggning mellan flera appar från samma utgivare.

På iOS lägger du till `com.microsoft.adalcache` nyckel rings gruppen till appens rättighet i XCode under **projekt inställningar** > **funktioner** > **nyckel rings delning**

### <a name="macos"></a>macOS

MSAL på macOS använder `com.microsoft.identity.universalstorage` åtkomst grupp som standard.

På grund av begränsningar för nyckel ringar av macOS, översätts MSAL-`access group` inte direkt till åtkomst gruppens attribut för nyckel ringar (se [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) på MacOS 10,14 och tidigare. Det fungerar dock på samma sätt från ett SSO-perspektiv genom att se till att flera program som distribueras av samma Apple-utvecklare kan ha tyst SSO.

På macOS 10,15 och senare (macOS Catalina) använder MSAL-attributet för nyckel ringar för att uppnå tyst SSO, på samma sätt som för iOS.

## <a name="custom-keychain-access-group"></a>Anpassad nyckel rings åtkomst grupp

Om du vill använda en annan åtkomst grupp för nyckel ringar kan du skicka en anpassad grupp när du skapar `MSALPublicClientApplicationConfig` innan du skapar `MSALPublicClientApplication`, så här:

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Inaktivera delning av nyckel ringar

Om du inte vill dela SSO-tillstånd mellan flera appar eller använda någon åtkomst grupp för nyckel ringar, inaktiverar du delning av nyckel ringar genom att skicka programpaket-ID: t som din keychainGroup:

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Referens-34018-fel (det gick inte att ange objekt i nyckel ringen)

Fel – 34018 innebär vanligt vis att nyckel ringen inte har kon figurer ATS korrekt. Se till att åtkomst gruppen för nyckel ringar som har kon figurer ATS i MSAL matchar den som kon figurer ATS i rättigheter.

## <a name="ensure-your-application-is-properly-signed"></a>Se till att ditt program är korrekt signerat

I macOS kan program köras utan att vara signerade av utvecklaren. Även om de flesta av MSAL-funktionerna fortsätter att fungera måste programmet vara signerat via SSO via nyckel åtkomst. Om du har flera prompter för nyckel ringar kontrollerar du att programmets signatur är giltig.

## <a name="next-steps"></a>Nästa steg

Läs mer om nyckel rings åtkomst grupper i Apples [delnings åtkomst till nyckel ringar i en samling av apps-artiklar](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
