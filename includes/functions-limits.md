---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: eb61bf5734d0aea1534735b23a2b95b52000f5ad
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322911"
---
| Resource | [Förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium-plan](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service plan](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Skala ut | Händelse driven | Händelse driven | [Manuell/autoskalning](../articles/app-service/manage-scale-up.md) | 
| Max. antal instanser | 200 | 100 | 10-20 |
|[Tids gräns](../articles/azure-functions/functions-scale.md#timeout) för standard tid (min) |5 | 30 |30<sup>2</sup> |
|Max [time-out-varaktighet](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | 60 | obegränsat<sup>3</sup> |
| Max. utgående anslutningar (per instans) | 600 aktiv (1200 totalt) | unbounded | unbounded |
| Max storlek för begäran (MB)<sup>4</sup> | 100 | 100 | 100 |
| Max. frågesträngs längd<sup>4</sup> | 4096 | 4096 | 4096 |
| Max. URL-längd för begäran<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per instans | 100 | 210-840 | 100-840 |
| Maximalt minne (GB per instans) | 1.5 | 3.5-14 | 1,75-14 |
| Function-appar per plan |100 |100 |obegränsad<sup>5</sup> |
| [App Service-planer](../articles/app-service/overview-hosting-plans.md) | 100 per [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resurs grupp |100 per resurs grupp |
| Lagring<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Anpassade domäner per app</a> |500<sup>7</sup> |500 |500 |
| Stöd för anpassad domän- [SSL](../articles/app-service/configure-ssl-bindings.md) |en obunden SNI SSL anslutning ingår | obegränsade SNI SSL och 1 IP SSL anslutningar ingår |obegränsade SNI SSL och 1 IP SSL anslutningar ingår | 

<sup>1</sup> vissa gränser för de olika App Service plan alternativen finns i [App Service plan gränser](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> som standard är tids gränsen för funktioner 1. x-körningsmiljön i en app service plan obegränsad.  
<sup>3</sup> kräver att App Service plan anges till [Always on](../articles/azure-functions/functions-scale.md#always-on). Betala enligt [standardpriser](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> de här gränserna [anges på värden](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> det faktiska antalet funktions program som du kan vara värd för beror på aktivitetens appar, storleken på dator instanserna och motsvarande resursutnyttjande.  
<sup>6</sup> lagrings gränsen är den totala innehålls storleken i tillfällig lagring över alla appar i samma app service plan. Förbruknings planen använder Azure Files för tillfällig lagring.  
<sup>7</sup> när din Function-app finns i en [förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan)stöds endast alternativet CNAME. För Function-appar i en [Premium-plan](../articles/azure-functions/functions-scale.md#premium-plan) eller en [App Service plan](../articles/azure-functions/functions-scale.md#app-service-plan)kan du mappa en anpassad domän med antingen en CNAME-eller en a-post.
