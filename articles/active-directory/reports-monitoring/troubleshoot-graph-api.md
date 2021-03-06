---
title: Felsöka fel i API för Azure Active Directory rapportering | Microsoft Docs
description: 'Ger en lösning på fel när du anropar Azure Active Directory rapporterings-API: er.'
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989539"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Felsöka fel i Azure Active Directory rapporterings-API

Den här artikeln innehåller vanliga fel meddelanden som du kan köra i när du får åtkomst till aktivitets rapporter med hjälp av MS Graph API och steg för att lösa problemet.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internt Server fel vid åtkomst till Microsoft Graph v2-slutpunkt

Vi stöder för närvarande inte Microsoft Graph v2-slutpunkten – se till att få åtkomst till aktivitets loggarna med hjälp av Microsoft Graph v1-slutpunkten.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Fel: Det gick inte att hämta användar roller från AD Graph

Du kan få det här fel meddelandet när du försöker få åtkomst till inloggningar med Graph Explorer. Kontrol lera att du är inloggad på ditt konto med hjälp av båda inloggnings knapparna i graphs användar gränssnitt, som du ser i följande bild. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Fel: Det gick inte att kontrol lera Premium-licens från AD Graph 

Om du stöter på det här fel meddelandet när du försöker få åtkomst till inloggningar med Graph Explorer väljer du **ändra behörigheter** under ditt konto i det vänstra navigerings fältet och väljer **Tasks. readwrite** och **Directory. Read. all**. 

![Ändra behörighets gränssnitt](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Ingen klient organisation är B2C eller klient organisationen har ingen Premium-licens

Åtkomst till inloggnings rapporter kräver en licens för Azure Active Directory Premium 1 (P1). Om du ser det här fel meddelandet vid åtkomst till inloggningar kontrollerar du att klienten är licensierad med en Azure AD P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren finns inte i de tillåtna rollerna 

Om du ser det här fel meddelandet när du försöker få åtkomst till gransknings loggar eller inloggningar med hjälp av API: et kontrollerar du att ditt konto är en del av rollen **säkerhets läsare** eller **rapport läsare** i Azure Active Directory klient organisationen. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Program som saknar AAD-behörighet för Läs katalog data 

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Fel: Program saknar MSGraph API ' Läs alla Gransknings logg data ' behörighet

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

## <a name="next-steps"></a>Nästa steg

[Använd gransknings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
-referensen[Använd rapport-API-referensen för inloggnings aktivitet](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
