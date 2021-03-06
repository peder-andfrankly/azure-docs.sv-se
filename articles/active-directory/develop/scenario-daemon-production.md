---
title: 'Flytta daemon-appen som anropar webb-API: er till produktion – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du flyttar en daemon-app som anropar webb-API: er till produktion'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961928499008445207df3a34a51fc016723c294b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962601"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app som anropar webb-API: er – flytta till produktion

Nu när du vet hur man hämtar och använder en token för ett tjänst-till-tjänst-anrop, lär du dig hur du flyttar din app till produktion.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Distribution – fall av daemon-appar för flera innehavare

Om du är en ISV som skapar ett daemon-program som kan köras i flera klienter måste du se till att klient administratörerna:

- Tillhandahåller ett tjänst huvud namn för programmet
- Ger medgivande till programmet

Du måste förklara vad kunderna har för att utföra dessa åtgärder. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

Här följer några länkar för att lära dig mer:

# <a name="nettabdotnet"></a>[NET](#tab/dotnet)

- Om du inte redan har gjort det kan du prova snabb starten [Hämta en token och anropa Microsoft Graph API från en konsol app med appens identitet](./quickstart-v2-netcore-daemon.md).
- Referens dokumentation för:
  - Instansiera [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Anropar [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Andra exempel/Självstudier:
  - [Microsoft-Identity-Platform-Console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) innehåller ett enkelt .net Core daemon-konsolprogram som visar användare av en klient som frågar Microsoft Graph.

    ![topologi](media/scenario-daemon-app/daemon-app-sample.svg)

    Samma exempel illustrerar också variationen med certifikat.

    ![topologi](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) har ett ASP.NET MVC-webbprogram som synkroniserar data från Microsoft Graph att använda identiteten för programmet i stället för en användares räkning. Exemplet illustrerar även godkännande processen för administratörer.

    ![topologi](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

Försök att [Hämta en token med snabb start och anropa Microsoft Graph API från en python-konsol app med appens identitet](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java är för närvarande en offentlig för hands version. Mer information finns i [MSAL java dev-exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
