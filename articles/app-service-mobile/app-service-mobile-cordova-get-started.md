---
title: Skapa en Cordova-app
description: Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Apache Cordova-utveckling
keywords: cordova, javascript, mobil, klient
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 35670543b9716cad8165d5b5ea48f3888786f0a2
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668886"
---
# <a name="create-an-apache-cordova-app"></a>Skapa en Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Apache Cordova-mobilapp med hjälp av en mobilappserverdel i Azure.  Du skapar både en ny serverdel för mobilappen och en enkel Apache Cordova-app med en *att göra-lista* som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Apache Cordova-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Krav
För att slutföra den här självstudien, finns följande förhandskrav:

* En PC-dator med [Visual Studio Community 2017] eller senare.
* [Visual Studio Tools för Apache Cordova].
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Du kan också kringgå Visual Studio och direkt använda kommandoraden i Apache Cordova.  Det är praktiskt att använda kommandoraden om du går självstudiekursen på en Mac-dator.  I den här kursen ingår inte att kompilera klientprogram i Apache Cordova med hjälp av kommandoraden.

## <a name="create-an-azure-mobile-app-backend"></a>Skapa serverdelen för en Azure-mobilapp
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databas anslutning och konfigurera klient-och Server projektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Hämta och köra Apache Cordova-appen
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
