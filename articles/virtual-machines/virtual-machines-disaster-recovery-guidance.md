---
title: Katastrof återställnings scenarier för virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du gör i händelse av att ett Azure-tjänst avbrott påverkar Azure Virtual Machines.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b59343fca5e14d211864a1354001819c918bd1ca
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891525"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Vad du gör om ett avbrott i Azure-tjänsten påverkar virtuella Azure-datorer
På Microsoft arbetar vi hårt för att se till att våra tjänster alltid är tillgängliga när du behöver dem. Det kommer inte längre att påverka vår kontroll på sätt som orsakar oplanerade avbrott i tjänsten.

Microsoft tillhandahåller en Serviceavtal (SLA) för sina tjänster som ett åtagande för drift tid och anslutning. Service avtalet för enskilda Azure-tjänster finns på [Azure Service nivå avtal](https://azure.microsoft.com/support/legal/sla/).

Azure har redan många inbyggda plattforms funktioner som stöder program med hög tillgänglighet. Läs [haveri beredskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)om du vill ha mer information om dessa tjänster.

Den här artikeln beskriver ett verkligt haveri återställnings scenario, när en hel region upplever ett avbrott på grund av stor natur katastrof eller omfattande avbrott i tjänsten. Detta är ovanliga förekomster, men du måste förbereda dig för möjligheten att det uppstår ett avbrott i en hel region. Om en hel region upplever avbrott i tjänsten blir de lokalt redundanta kopiorna av dina data tillfälligt otillgängliga. Om du har aktiverat geo-replikering lagras tre ytterligare kopior av dina Azure Storage blobbar och tabeller i en annan region. I händelse av ett fullständigt regionalt avbrott eller en katastrof där den primära regionen inte kan återskapas mappar Azure om alla DNS-poster till den geo-replikerade regionen.

För att hjälpa dig att hantera dessa sällsynta förekomster ger vi följande vägledning för virtuella Azure-datorer om ett avbrott uppstår i hela regionen där ditt Azure Virtual Machine-program distribueras.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Alternativ 1: initiera en redundansväxling med hjälp av Azure Site Recovery
Du kan konfigurera Azure Site Recovery för dina virtuella datorer så att du kan återställa ditt program med ett enda klick i flera minuter. Du kan replikera till en valfri Azure-region och inte begränsad till kopplade regioner. Du kan komma igång genom att [Replikera dina virtuella datorer](https://aka.ms/a2a-getting-started). Du kan [skapa en återställnings plan](../site-recovery/site-recovery-create-recovery-plans.md) så att du kan automatisera hela redundansväxlingen för ditt program. Du kan [testa redundansväxlingen i](../site-recovery/site-recovery-test-failover-to-azure.md) förväg utan att påverka produktions programmet eller den pågående replikeringen. Om det uppstår ett avbrott i den primära regionen initierar du bara [en redundansväxling](../site-recovery/site-recovery-failover.md) och tar ditt program i mål regionen.


## <a name="option-2-wait-for-recovery"></a>Alternativ 2: vänta på återställning
I det här fallet krävs ingen åtgärd på din del. Se till att vi arbetar hårt för att återställa tjänstens tillgänglighet. Du kan se aktuell status för tjänsten på vår [Azure Service Health-instrumentpanel](https://azure.microsoft.com/status/).

Det här är det bästa alternativet om du inte har konfigurerat Azure Site Recovery, Geo-redundant lagring med Läs behörighet eller Geo-redundant lagring före avbrott. Om du har konfigurerat Geo-redundant lagring eller Geo-redundant lagring med Läs åtkomst för lagrings kontot där virtuella hård diskar för virtuella datorer lagras, kan du se till att återställa bas avbildningens virtuella hård disk och försöka etablera en ny virtuell dator från den. Detta är inte ett önskat alternativ eftersom det inte finns några garantier för synkronisering av data. Det här alternativet är därför inte garanterat att fungera.


> [!NOTE]
> Tänk på att du inte har någon kontroll över den här processen och att det bara inträffar för hela verksamhets störningar. Därför måste du också förlita dig på andra programspecifika säkerhets kopierings strategier för att uppnå den högsta tillgängliga tillgänglighets nivån. Mer information finns i avsnittet om [data strategier för haveri beredskap](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan).
>
>

## <a name="next-steps"></a>Nästa steg

- Börja [skydda dina program som körs på virtuella Azure-datorer](https://aka.ms/a2a-getting-started) med Azure Site Recovery

- Mer information om hur du implementerar en strategi för haveri beredskap och hög tillgänglighet finns i [haveri beredskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Information om hur du utvecklar en detaljerad teknisk förståelse av moln plattformens funktioner finns i [teknisk vägledning för Azure-återhämtning](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).


- Om anvisningarna inte är tydliga, eller om du vill att Microsoft ska utföra åtgärderna för din räkning, kontaktar du [kund support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
