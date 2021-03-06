---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 12/06/2019
ms.openlocfilehash: c3eeb59809a4028702022e8a18db633a26b01d5c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981119"
---
1. I [Azure Portal](https://ms.portal.azure.com/)väljer du **Azure Active Directory** > **Appregistreringar** > **ny registrering**.

   [![ny program registrering i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Din app visas här när du har registrerat den.

1. Ge programmet ett namn och välj **konton i den här organisations katalogen endast** för att ange de **konto typer som stöds** och som kan komma åt API: et. Välj en giltig URI för att omdirigera användare till efter att de autentiserats och **Registrera dig**sedan.

   [![skapa programmet i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Viktig information om Azure Active Directory-appen visas i bladet **Översikt** för appen. Välj din app under **ägda program**och sedan på **Översikt**.

   [![kopiera program-ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiera ditt **program (klient)-ID** som ska användas i klient programmet.

1. Bladet **autentisering** anger viktiga konfigurations inställningar för autentisering. 

    1. **Omdirigerings-URI: er** måste matcha adressen som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklings miljö väljer du **offentlig klient (mobil & Desktop)** . Se till att ange **standard klient typ** till Ja.
        * För appar på en sida som finns på Azure App Service väljer du **webb**.

    1. Aktivera det implicita tilldelnings flödet genom att kontrol lera **åtkomsttoken** eller **ID-token**.

   [![skapa en ny klient hemlighet](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klicka på **Save** (Spara).

1. Välj **certifikat & hemligheter** och sedan **ny klient hemlighet** för att skapa ett program lösen ord som klienten kan använda för att bevisa sin identitet.

   [![skapa en ny klient hemlighet](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ditt klient hemliga lösen ord visas sedan. Kopiera nyckeln till din favorit text redigerare.

   > [!NOTE]
   > Du kan importera ett certifikat i stället. Ett certifikat rekommenderas för förbättrad säkerhet. Om du vill använda ett certifikat väljer du **Ladda upp certifikat**.

1. Associera din Azure Active Directory app Azure TIme Series Insights. Välj **API-behörigheter** > **Lägg till en behörighet** > - **API: er som används i organisationen**. 

    [![associera ett API med din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Skriv `Azure Time Series Insights` i Sök fältet och välj sedan `Azure Time Series Insights`.

1. Ange sedan den typ-API-behörighet som din app kräver. Som standard är **delegerade behörigheter** markerade. Välj en behörighets typ och välj sedan **Lägg till behörigheter**.

    [![ange vilken typ av API-behörighet din app kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)