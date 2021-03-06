---
title: Flytta Azure App Service resurser
description: Använd Azure Resource Manager för att flytta App Service resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150892"
---
# <a name="move-guidance-for-app-service-resources"></a>Flytta vägledning för App Service resurser

Stegen för att flytta App Service resurser skiljer sig åt beroende på om du flyttar resurserna i en prenumeration eller till en ny prenumeration.

## <a name="move-in-same-subscription"></a>Flytta i samma prenumeration

När du flyttar en webbapp _inom samma prenumeration_kan du inte flytta SSL-certifikat från tredje part. Du kan dock flytta en webbapp till den nya resurs gruppen utan att flytta dess certifikat från tredje part, och appens SSL-funktioner fungerar fortfarande.

Om du vill flytta SSL-certifikat med Webbappen gör du följande:

1. Ta bort certifikatet från en tredje part från webbappen, men Behåll en kopia av ditt certifikat
2. Flytta Webbappen.
3. Överför certifikatet från tredje part till den flyttade webbappen.

## <a name="move-across-subscriptions"></a>Flytta över prenumerationer

När du flyttar en Webbapp _mellan prenumerationer_, gäller följande begränsningar:

- Målresursgruppen får inte ha några befintliga App Service-resurser. App Service-resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service-resurser i resursgruppen måste flyttas tillsammans.
- App Service-resurser kan bara flyttas från resursgruppen där de skapades. Om en App Service resurs inte längre finns i den ursprungliga resurs gruppen flyttar du tillbaka den till den ursprungliga resurs gruppen. Flytta sedan resursen över prenumerationer.

Om du inte kommer ihåg den ursprungliga resurs gruppen kan du hitta den via diagnostik. För din webbapp väljer du **diagnostisera och lösa problem**. Välj sedan **konfiguration och hantering**.

![Välj diagnostik](./media/app-service-move-limitations/select-diagnostics.png)

Välj **alternativ för migrering**.

![Välj alternativ för migrering](./media/app-service-move-limitations/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webb programmet.

![Välj rekommenderade steg](./media/app-service-move-limitations/recommended-steps.png)

Du ser de rekommenderade åtgärderna innan du flyttar resurserna. Informationen omfattar den ursprungliga resurs gruppen för webbappen.

![Rekommendationer](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Flytta App Service Certificate

Du kan flytta App Service Certificate till en ny resurs grupp eller prenumeration. Om din App Service Certificate är kopplad till en webbapp måste du vidta några steg innan du flyttar resurserna till en ny prenumeration. Ta bort SSL-bindningen och det privata certifikatet från webbappen innan du flyttar resurserna. App Service Certificate behöver inte tas bort, bara det privata certifikatet i webbappen.

## <a name="move-support"></a>Stöd för att flytta

Information om hur du tar reda på vilka App Service resurser som kan flyttas finns i flytta support status för:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../resource-group-move-resources.md).
