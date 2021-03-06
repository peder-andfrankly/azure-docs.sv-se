---
title: Avpublicera Azure Sentinel | Microsoft Docs
description: Ta bort din Azure Sentinel-instans.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777442"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Ta bort Azure Sentinel från din arbets yta

Om du inte längre vill använda Azure Sentinel förklarar den här artikeln hur du tar bort den från din arbets yta.

## <a name="how-to-delete-azure-sentinel"></a>Ta bort Azure Sentinel

När du installerar Azure Sentinel i bakgrunden installeras **SecurityInsights** -lösningen på den valda arbets ytan. Det första du behöver göra är att ta bort **SecurityInsights** -lösningen.

1.  Gå till **Azure Sentinel**, följt av **konfigurationen**, följt av **inställningarna för arbets ytan**och sedan **lösningar**.

2.  Välj `SecurityInsights` och klicka på den.

    ![Hitta SecurityInsights-lösningen](media/offboard/find-solution.png)

3.  Längst upp på sidan väljer du **ta bort**.

    > [!IMPORTANT]
    > Om du tar bort arbets ytan kan det påverka andra lösningar och data källor som använder den här arbets ytan, inklusive Azure Monitor. Om du vill kontrol lera vilka lösningar som använder den här arbets ytan går du till [lista installerade övervaknings lösningar](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Om du vill kontrol lera vilka lösningar för data som matas in i arbets ytan, se förstå inmatad [data volym](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume).

    ![Ta bort SecurityInsights-lösningen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Vad händer i bakgrunden?

När du tar bort lösningen tar Azure Sentinel upp till 48 timmar innan den första fasen av borttagnings processen slutförs.

När från kopplingen har identifierats börjar offboarding-processen.

**Konfigurationen av dessa anslutningar tas bort:**
-   Office 365

-   AWS

-   Säkerhets aviseringar från Microsoft Services (Azure ATP, Microsoft Cloud App Security inklusive Cloud Discovery Shadow IT repor ting, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Hotinformation

-   Vanliga säkerhets loggar (inklusive CEF-baserade loggar, Barracuda och syslog) (om du har Azure Security Center kommer dessa loggar att fortsätta att samlas in.)

-   Windows säkerhets händelser (om du har Azure Security Center fortsätter dessa loggar att samlas in.)

Inom de första 48 timmarna kommer data och aviserings regler (inklusive konfiguration av real tids automatisering) inte längre att vara tillgängliga eller efter frågas i Azure Sentinel.

**Efter 30 dagar tas dessa resurser bort:**

-   Incidenter (inklusive undersöknings-metadata)

-   Varningsregler

-   dina

Din spel böcker, sparade arbets böcker, sparade jakt frågor och antecknings böcker tas inte bort. **Vissa kan brytas på grund av borttagna data. Du kan ta bort dem manuellt.**

När du har tagit bort tjänsten finns det en respitperiod på 30 dagar då du kan återaktivera lösningen och dina data och varnings regler återställs, men de konfigurerade anslutningarna som kopplades från måste återanslutas.

> [!NOTE]
> Om du tar bort lösningen fortsätter prenumerationen att registreras med Azure Sentinel Resource Provider. **Du kan ta bort den manuellt.**




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du tar bort Azure Sentinel-tjänsten. Om du ändrar dig och vill installera det igen:
- Kom igång [med att använda Azure Sentinel](quickstart-onboard.md).

