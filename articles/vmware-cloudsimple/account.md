---
title: Konto hantering – Azure VMware-lösning av CloudSimple-portalen
description: Beskriver hur du hanterar konton på Azure VMware-lösningen från CloudSimple-portalen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564191"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Hantera konton på Azure VMware-lösningen från CloudSimple-portalen

När du skapar din CloudSimple-tjänst skapas ett konto på CloudSimple. Kontot är associerat med din Azure-prenumeration där tjänsten finns. Alla användare med rollen ägare och deltagare i prenumerationen har åtkomst till CloudSimple-portalen. Det ID för Azure-prenumeration och klient-ID som är associerat med CloudSimple-tjänsten finns på konto sidan.

Om du vill hantera konton i CloudSimple-portalen går [du till portalen](access-cloudsimple-portal.md) och väljer **konto** på menyn på sidan.

Välj **Sammanfattning** om du vill visa information om ditt företags CloudSimple-konfiguration. Den aktuella kapaciteten för moln konfigurationen visas, inklusive antal privata moln, totalt lagrings utrymme, kluster konfiguration för vSphere, antal noder och antalet beräknings kärnor. En länk ingår för att köpa ytterligare noder om den aktuella konfigurationen inte uppfyller alla dina behov.

## <a name="email-alerts"></a>E-postaviseringar

Du kan lägga till e-postadresser till alla personer som du vill meddela om ändringar i konfigurationen för det privata molnet.

1. Klicka på **Lägg till nytt**i avsnittet **ytterligare e-** postaviseringar.
2. Ange e-postadressen.
3. Tryck på RETUR.  

Klicka på **X**om du vill ta bort en post.

## <a name="cloudsimple-operator-access"></a>Åtkomst till CloudSimple-operatör

Med åtkomst inställningen operatör kan CloudSimple hjälpa dig med fel sökning genom att tillåta en support tekniker att logga in på din CloudSimple-Portal.  Inställningen är aktive rad som standard. Alla åtgärder som utförs av support teknikern när de är inloggade på ditt kund konto registreras och är tillgängliga för din granskning på sidan **aktivitets** > **granskning** .

Klicka på **CloudSimple operatörs åtkomst aktive rad** växla för att aktivera eller inaktivera åtkomst.
