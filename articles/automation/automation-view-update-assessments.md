---
title: Visa Azure Uppdateringshantering Update-utvärderingar
description: Den här artikeln beskriver hur du visar uppdaterings utvärderingar för uppdaterings distributioner.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d27df57e9371f16a15d3a18b7722598062377d88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850524"
---
# <a name="view-azure-update-management-update-assessments"></a>Visa Azure Uppdateringshantering Update-utvärderingar

I ditt Azure Automation konto väljer du **uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterings distributioner och schemalagda uppdaterings distributioner. I kolumnen **efterlevnad** kan du se den senaste gången datorn utvärderades. I kolumnen **Uppdatera agent beredskap** kan du se uppdaterings agentens hälso tillstånd. Om ett problem uppstår väljer du länken för att gå till fel söknings dokumentation som kan hjälpa dig att åtgärda problemet.

Om du vill köra en loggs ökning som returnerar information om datorn, uppdateringen eller distributionen väljer du motsvarande objekt i listan. Fönstret **loggs ökning** öppnas med en fråga för det valda objektet:

![Uppdateringshantering standardvy](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visa saknade uppdateringar

Välj **saknade uppdateringar** om du vill visa en lista med uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdatering, operativ Systems information och en länk för mer information visas. I fönstret **loggs ökning** visas även mer information om uppdateringarna.

![Uppdateringar som saknas](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Uppdatera klassificeringar

I följande tabeller visas uppdaterings klassificeringarna i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

### <a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska eller som inte är av säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data. (Denna granularitet är möjlig på grund av data berikning i molnet.) Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionerna av produkten. Om du har CentOS-datorer som har kon figurer ATS för att returnera säkerhets data för följande kommando kan Uppdateringshantering korrigeras baserat på klassificeringar:

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För närvarande erbjuds kunder som har aktiverat den här funktionen på egen hand support.

## <a name="next-steps"></a>Nästa steg

När du har granskat uppdaterings utvärderingen kan du schemalägga en uppdaterings distribution genom att följa stegen i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
