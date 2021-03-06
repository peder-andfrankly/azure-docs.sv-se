---
title: Fråga efter data från en GA- C# miljö med hjälp av kod-Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du frågar efter data från en Azure Time Series Insights miljö med hjälp av en C#anpassad app som skrivits i.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3729bedf7591ffecc558b88660486f7e336fa717
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705898"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Fråga efter data från Azure Time Series Insights GA-miljön med hjälp avC#

Det C# här exemplet visar hur du frågar efter data från Azure Time Series Insights ga-miljön.

Exemplet visar flera grundläggande exempel på användning av fråge-API:

1. Som förberedelse steg hämtar du åtkomsttoken via Azure Active Directory-API: et. Skicka denna token i `Authorization`-rubriken för varje fråge-API-begäran. Information om hur du konfigurerar icke-interaktiva program finns i [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md). Se också till att alla konstanter som definieras i början av exemplet är korrekt inställda.
1. Listan över miljöer som användaren har åtkomst till hämtas. En av miljöerna hämtas som en miljö av intresse och ytterligare data efter frågas i den här miljön.
1. Som ett exempel på en HTTPS-begäran begärs tillgänglighetsdata för den intressanta miljön.
1. Som ett exempel på en webbsocket-begäran begärs händelsens aggregerade data för den intressanta miljön. Data krävs för hela tillgänglighetstidsintervallet.

> [!NOTE]
> Exempel koden finns på [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Projekt beroenden

Lägg till NuGet-paket `Microsoft.IdentityModel.Clients.ActiveDirectory` och `Newtonsoft.Json`.

## <a name="c-example"></a>C#exempel

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Nästa steg

- Läs mer om frågor i [API-referensen för frågor](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Läs hur du [ansluter en JavaScript-app med hjälp av klient-SDK](https://github.com/microsoft/tsiclient) för att Time Series Insights.