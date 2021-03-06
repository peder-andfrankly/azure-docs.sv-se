---
title: Kända problem med Azure Monitor for VMs (för hands version) | Microsoft Docs
description: Den här artikeln beskriver kända problem med Azure Monitor for VMs, en lösning i Azure som kombinerar hälsa, identifiering av program beroenden och prestanda övervakning av operativ systemet för virtuella Azure-datorer.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.openlocfilehash: f6719a8c28571faceb6ebad0567d13a4edc60fe6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553771"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Kända problem med Azure Monitor for VMs (för hands version)

Den här artikeln beskriver kända problem med Azure Monitor for VMs, en lösning i Azure som kombinerar hälsa, identifiering av program komponenter och prestanda övervakning av operativ systemet för virtuella Azure-datorer. 

## <a name="health"></a>Hälso- och sjukvård 
Följande är kända problem med den aktuella versionen av hälso funktionen:

- Om en virtuell Azure-dator tas bort eller tas bort visas den i vyn VM-lista för en stund. Genom att klicka på tillståndet för en borttagen eller borttagen virtuell dator öppnar du dessutom vyn **hälso diagnos** och initierar sedan en inläsnings slinga. Om du väljer namnet på den borttagna virtuella datorn öppnas ett fönster med ett meddelande om att den virtuella datorn har tagits bort.
- Konfigurations ändringar, till exempel uppdatering av ett tröskelvärde, tar upp till 30 minuter även om portalen eller API: et för arbets belastnings övervakning kan uppdateras direkt. 
- Hälso Diagnoss upplevelsen uppdateras snabbare än andra vyer. Informationen kan vara försenad när du växlar mellan dem. 
- För virtuella Linux-datorer har titeln på sidan som visar hälso villkoren för en enskild VM-vy hela domän namnet för den virtuella datorn i stället för det användardefinierade virtuella dator namnet. 
- När du har inaktiverat övervakning för en virtuell dator med någon av de metoder som stöds och du försöker distribuera den igen, bör du distribuera den på samma arbets yta. Om du väljer en annan arbets yta och försöker visa hälso tillståndet för den virtuella datorn kan det Visa inkonsekventa beteenden.
- När du har tagit bort lösnings komponenterna från din arbets yta kan du fortsätta att se hälso tillståndet från dina virtuella Azure-datorer. specifikt prestanda och mappa data när du navigerar till någon av vyerna i portalen. Data kommer slutligen att sluta visas från vyn prestanda och karta efter en stund. Hälso läget kommer dock fortfarande att Visa hälso status för dina virtuella datorer. Alternativet **prova nu** är bara tillgängligt för att återställas från prestanda-och kart visning.

## <a name="next-steps"></a>Nästa steg
Om du vill förstå kraven och metoderna för att aktivera övervakning av dina virtuella datorer kan du läsa [aktivera Azure Monitor for VMS](vminsights-enable-overview.md).
