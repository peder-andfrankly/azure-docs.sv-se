---
title: Säkerhets rekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet vägleder dig genom hur rekommendationer i Azure Security Center hjälper dig att skydda dina Azure-resurser och hålla dem kompatibla med säkerhets principer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 6a01ddbc733ce52c02e9abef2569adbfbd19c6a8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520738"
---
# <a name="security-recommendations-in-azure-security-center"></a>Säkerhetsrekommendationer i Azure Security Center 
I det här avsnittet beskrivs hur du visar och förstår rekommendationerna i Azure Security Center som hjälper dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en steg-för-steg-guide.
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhets rekommendationer?

Rekommendationer är åtgärder som du kan vidta för att skydda dina resurser.

Security Center regelbundet analysera säkerhets läget för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du tar bort dem.

Varje rekommendation ger dig följande:

- En kort beskrivning av vad som rekommenderas.
- De åtgärder som vidtas för att genomföra rekommendationen. <!-- In some cases, Quick Fix remediation is available. -->
- Vilka resurser behöver du för att utföra den rekommenderade åtgärden på dem.
- Den **säkra poängen**, vilket är det belopp som dina säkra poäng kommer att gå upp om du implementerar den här rekommendationen.

## Övervaka rekommendationer<a name="monitor-recommendations"></a>

Security Center analyserar dina resursers säkerhets tillstånd för att identifiera potentiella sårbarheter. Panelen **rekommendationer** under **Översikt** visar det totala antalet rekommendationer som identifieras av Security Center.

![Översikt över Security Center](./media/security-center-recommendations/asc-overview.png)

1. Välj **panelen rekommendationer** under **Översikt**. Listan **rekommendationer** öppnas.

      ![Visa rekommendationer](./media/security-center-recommendations/view-recommendations.png)

    Du kan filtrera rekommendationer. Om du vill filtrera rekommendationerna väljer du **filter** på bladet **rekommendationer** . **Filter** bladet öppnas och du väljer de allvarlighets grad och tillstånds värden som du vill se.

   * **Rekommendationer**: rekommendationen.
   * **Säker Poäng effekt**: en poäng som skapats av Security Center med hjälp av dina säkerhets rekommendationer och tillämpar avancerade algoritmer för att fastställa hur viktiga varje rekommendation är. Mer information finns i [säker Poäng beräkning](security-center-secure-score.md#secure-score-calculation).
   * **Resurs**: visar de resurser som den här rekommendationen gäller för.
   * **Statusfält**: beskriver allvarlighets graden för den specifika rekommendationen:
       * **Hög (röd)** : det finns ett säkerhets problem med en meningsfull resurs (till exempel ett program, en virtuell dator eller en nätverks säkerhets grupp) och kräver en åtgärd.
       * **Medel (orange)** : det finns ett säkerhets problem och icke-kritiska eller ytterligare steg krävs för att eliminera det eller slutföra en process.
       * **Låg (blå)** : det finns ett säkerhets problem som bör åtgärdas, men som inte kräver omedelbara åtgärder. (Som standard visas inte låga rekommendationer, men du kan filtrera efter låg rekommendation om du vill se dem.) 
       * **Felfritt (grönt)** :
       * **Inte tillgängligt (grått)** :

1. Klicka på rekommendationen om du vill visa information om varje rekommendation.

    ![Rekommendations information](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Se [klassiska distributions modeller för och Resource Manager](../azure-classic-rm.md) för Azure-resurser.
 
## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lanserat säkerhets rekommendationer i Security Center. Lär dig hur du åtgärdar rekommendationerna:

* [Åtgärda rekommendationer](security-center-remediate-recommendations.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
