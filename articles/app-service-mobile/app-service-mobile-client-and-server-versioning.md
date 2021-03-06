---
title: Hantering av klient-och Server-SDK
description: 'Lista över klient-SDK: er och kompatibilitet med Server SDK-versioner för Mobile Services och Azure Mobile Apps.'
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4d0b301dee363c2338cb13a9fc09ee17549467eb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668844"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Klient-och Server version i Mobile Apps och Mobile Services
> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

Den senaste versionen av Azure Mobile Services är den **Mobile Apps** funktionen i Azure App Service.

Mobile Apps klient-och Server-SDK: er baseras ursprungligen på dem i Mobile Services, men de är *inte* kompatibla med varandra.
Det innebär att du måste använda en *Mobile Apps* klient-SDK med ett *Mobile Apps* Server-SDK och på samma sätt för *Mobile Services*. Det här kontraktet upprätthålls genom ett särskilt huvud värde som används av klient-och Server-SDK: erna `ZUMO-API-VERSION`.

Obs: när det här dokumentet refererar till en *Mobile Services* Server del behöver det inte nödvändigt vis vara värd för Mobile Services. Nu kan du migrera en mobil tjänst så att den körs på App Service utan några kod ändringar, men tjänsten kommer fortfarande att använda *Mobile Services* SDK-versioner.

## <a name="header-specification"></a>Rubrik specifikation
Nyckel `ZUMO-API-VERSION` kan anges antingen i HTTP-huvudet eller frågesträngen. Värdet är en versions sträng i formatet **x. y. z**.

Exempel:

Hämta https://service.azurewebsites.net/tables/TodoItem

SIDHUVUD: ZUMO-API-VERSION: 2.0.0

PUBLICERA https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Väljer av versions kontroll
Du kan avanmäla versions kontrollen genom att ange värdet **Sant** för appens inställning **MS_SkipVersionCheck**. Ange detta antingen i Web. config eller i avsnittet program inställningar i Azure Portal.

> [!NOTE]
> Det finns ett antal funktions ändringar mellan Mobile Services och Mobile Apps, särskilt i områdena synkronisering, autentisering och push-meddelanden offline. Du bör endast välja versions kontroll efter fullständig testning för att se till att dessa beteende ändringar inte bryter din Apps funktioner.

## <a name="2.0.0"></a>Azure Mobile Apps-klient och-Server
### <a name="MobileAppsClients"></a>Klient-SDK: er för Mobile *Apps*
Versions kontroll introducerades från och med följande versioner av klient-SDK: n för **Azure Mobile Apps**:

| Klient plattform | Version | Värde för versions huvud |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Server-SDK: er för Mobile *Apps*
Versions kontroll ingår i följande Server SDK-versioner:

| Server plattform | SDK | Godkänt versions huvud |
| --- | --- | --- |
| .NET |[Microsoft. Azure. Mobile. Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure-Mobile-Apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Beteende för Mobile Apps Server delar
| ZUMO – API-VERSION | Värde för MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| x. y. z eller null |Sant |200 – OK |
| Null |Falskt/ej angivet |400 – felaktig begäran |
| 1. x. y |Falskt/ej angivet |400 – felaktig begäran |
| 2.0.0 – 2. x. y |Falskt/ej angivet |200 – OK |
| 3.0.0 – 3. x. y |Falskt/ej angivet |400 – felaktig begäran |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
