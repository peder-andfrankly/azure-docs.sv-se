---
title: Hämta en token tyst (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hämtar en åtkomsttoken tyst (från token cache) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c988eba278e184d0e60a4ede456a340c258e71
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915978"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Hämta en token från token-cachen med MSAL.NET

När du hämtar en åtkomsttoken med Microsoft Authentication Library för .NET (MSAL.NET) cachelagras token. När programmet behöver en token ska det först anropa `AcquireTokenSilent`-metoden för att kontrol lera om en acceptabel token finns i cacheminnet. I många fall är det möjligt att förvärva en annan token med fler omfång baserade på en token i cachen. Det är också möjligt att uppdatera en token när den snart upphör att gälla (eftersom token cache också innehåller en uppdateringstoken).

Det rekommenderade mönstret är att anropa metoden `AcquireTokenSilent` först.  Om `AcquireTokenSilent` Miss lyckas kan du hämta en token med andra metoder.

I följande exempel försöker programmet först hämta en token från token-cachen.  Om ett `MsalUiRequiredException` undantag genereras, kommer programmet att förvärva en token interaktivt. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
