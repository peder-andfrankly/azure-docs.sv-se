---
title: Skapa incidenter från aviseringar i Azure Sentinel | Microsoft Docs
description: Lär dig hur du skapar incidenter från aviseringar i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 722feb29a08cd5e1113eae197fbcae9769ed5550
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029760"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Skapa incidenter automatiskt från Microsofts säkerhets aviseringar

Aviseringar som har utlösts i Microsoft-säkerhetslösningar som är anslutna till Azure Sentinel, till exempel Microsoft Cloud App Security och Azure Advanced Threat Protection, skapar inte automatiskt incidenter i Azure Sentinel. När du ansluter en Microsoft-lösning till Azure Sentinel kommer alla aviseringar som genereras i den tjänsten att lagras som rå data i Azure Sentinel, i tabellen säkerhets avisering i Azure Sentinel-arbetsytan. Du kan sedan använda dessa data, t. ex. andra rå data som du ansluter till Sentinel.

Du kan enkelt konfigurera Azure Sentinel för att automatiskt skapa incidenter varje gång en avisering utlöses i en ansluten Microsoft-säkerhetslösning genom att följa anvisningarna i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
Du måste [ansluta Microsoft-säkerhetslösningar](connect-data-sources.md#data-connection-methods) för att aktivera skapande av incidenter från säkerhets tjänst aviseringar.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Använda analys regler för skapande av Microsofts säkerhets incidenter

Använd de inbyggda regler som är tillgängliga i Azure Sentinel för att välja vilka anslutna Microsoft-säkerhetslösningar som ska skapa Azure Sentinel-incidenter automatiskt i real tid. Du kan också redigera reglerna för att definiera mer detaljerade alternativ för filtrering som av de aviseringar som genereras av Microsoft Security-lösningen ska skapa incidenter i Azure Sentinel. Du kan till exempel välja att skapa Azure Sentinel-incidenter automatiskt från hög allvarlighets grad Azure Security Center aviseringar.

1. I Azure Portal under Azure Sentinel väljer du **analys**.

1. Välj fliken **regelmallar** för att se alla de inbyggda analys reglerna.

    ![Regelmallar](media/incidents-from-alerts/rule-templates.png)

1. Välj den regel mal len för **Microsoft Security** Analytics som du vill använda och klicka på **Skapa regel**.

    ![Security Analytics-regel](media/incidents-from-alerts/security-analytics-rule.png)

1. Du kan ändra regel informationen och välja att filtrera de aviseringar som ska skapa incidenter efter allvarlighets grad eller text som finns i aviseringens namn.  
      
    Om du till exempel väljer **Azure Security Center** i fältet **Microsoft Security Service** och väljer **hög** i fältet **Filtrera efter allvarlighets grad** , skapar bara incidenter med hög allvarlighets grad Azure Security Center aviseringar automatiskt incidenter i Azure Sentinel.  

    ![Guiden Skapa regel](media/incidents-from-alerts/create-rule-wizard.png)

1. Du kan också skapa en ny **säkerhets** regel för Microsoft som filtrerar aviseringar från olika Microsoft-säkerhetstjänster genom att klicka på **+ skapa** och välja regel för att **skapa en Microsoft-incident**.

    ![Regel för skapande av incident](media/incidents-from-alerts/incident-creation-rule.png)

  Du kan skapa mer än en **Microsoft Security** analytisk regel per typ av **säkerhets tjänst i Microsoft** . Detta skapar inga dubbla incidenter eftersom varje regel används som ett filter. Även om en avisering matchar mer än en regel för **Microsoft** -säkerhetsanalys skapas bara en Azure Sentinel-incident.

## <a name="enable-incident-generation-automatically-during-connection"></a>Aktivera automatisk generering av incidenter under anslutning
 När du ansluter en Microsoft-säkerhetslösning kan du välja om du vill att aviseringarna från säkerhets lösningen automatiskt ska generera incidenter i Azure Sentinel automatiskt.

1. Anslut en data källa för Microsoft Security-lösningen. 

   ![Generera säkerhets incidenter](media/incidents-from-alerts/generate-security-incidents.png)

1. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
