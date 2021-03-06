---
title: Använd dynamiska grupper med Azure Uppdateringshantering
description: Den här artikeln beskriver hur dynamiska grupper fungerar med Azure Automation Uppdateringshantering.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 022c2061febb54666acee1cfed4ec595421660a3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278721"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Uppdateringshantering ger möjlighet att rikta en dynamisk grupp med virtuella Azure-datorer eller icke-Azure-datorer för uppdaterings distributioner. Dessa grupper utvärderas vid distributions tiden så att du inte behöver redigera distributionen för att lägga till datorer.

## <a name="azure-machines"></a>Azure-datorer

Dessa grupper definieras av en fråga, när en uppdaterings distribution börjar, utvärderas medlemmarna i gruppen. Dynamiska grupper fungerar inte med klassiska virtuella datorer. När du definierar din fråga kan följande objekt användas tillsammans för att fylla i den dynamiska gruppen:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Om du vill förhandsgranska resultatet av en dynamisk grupp klickar du på knappen **Förhandsgranska** . Den här för hands versionen visar grupp medlemskapet vid denna tidpunkt, i det här exemplet söker vi efter datorer med tag- **rollen** är lika med **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner för den gruppen.

![för hands versions grupper](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Datorer som inte är Azure-datorer

För datorer som inte är Azure-datorer används sparade sökningar även som dator grupper för att skapa den dynamiska gruppen. Information om hur du skapar en sparad sökning finns i [skapa en dator grupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När gruppen har skapats kan du välja den i listan över sparade sökningar. Klicka på **Förhandsgranska** för att förhandsgranska datorerna i den sparade sökningen vid detta tillfälle.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat en dynamisk grupp kan du [skapa en uppdaterings distribution](automation-tutorial-update-management.md)
