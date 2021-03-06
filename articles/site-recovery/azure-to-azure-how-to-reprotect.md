---
title: Skydda virtuella Azure-datorer till den primära regionen med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du skyddar virtuella Azure-datorer efter en redundansväxling, den sekundära till den primära regionen med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954194"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Återaktivering av skydd på virtuella Azure-datorer till den primära regionen


När du [växlar över](site-recovery-failover.md) virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md), startar de virtuella datorerna i den sekundära regionen i oskyddat tillstånd. Om återställa de virtuella datorerna till den primära regionen måste du göra följande:

- Skydda de virtuella datorerna i den sekundära regionen så att de börjar replikera till den primära regionen.
- När skyddet har slutförts och de virtuella datorerna replikeras kan du växla över från den sekundära till den primära regionen.

## <a name="prerequisites"></a>Krav
1. Den virtuella datorns redundans från den primära till den sekundära regionen måste allokeras.
2. Den primära mål platsen måste vara tillgänglig och du bör kunna komma åt eller skapa resurser i den regionen.

## <a name="reprotect-a-vm"></a>Återaktivera skydd för en virtuell dator

1. I **valv** > **replikerade objekt**högerklickar du på den misslyckade virtuella datorn och väljer **sedan skydda igen**. Skydds riktningen bör visas från sekundär till primär.

   ![Skydda](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Granska resurs grupp, nätverk, lagring och tillgänglighets uppsättningar. Klicka på **OK**. Om det finns resurser som marker ATS som nya skapas de som en del av återställnings processen.
3. Återskydds jobbet dirigerar mål platsen med den senaste informationen. När detta har slutförts sker delta replikeringen. Sedan kan du växla tillbaka till den primära platsen. Du kan välja det lagrings konto eller det nätverk som du vill använda vid återaktivering med hjälp av alternativet Anpassa.

   ![Anpassa alternativ](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Anpassa inställningarna för att skydda på nytt

Du kan anpassa följande egenskaper för mål-VMe under återskydd.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap |Anteckningar  |
|---------|---------|
|Mål resurs grupp     | Ändra mål resurs gruppen som den virtuella datorn skapas i. Som en del av återskyddet tas den virtuella mål datorn bort. Du kan välja en ny resurs grupp under vilken du vill skapa den virtuella datorn efter redundansväxlingen.        |
|Virtuellt mål nätverk     | Det går inte att ändra mål nätverket under återskydds jobbet. Om du vill ändra nätverket gör du om nätverks mappningen.         |
|Mål lagring (sekundär virtuell dator använder inte hanterade diskar)     | Du kan ändra det lagrings konto som används av den virtuella datorn efter en redundansväxling.         |
|Hanterade replik diskar (sekundär virtuell dator använder Managed Disks)    | Site Recovery skapar en replik som Managed disks i den primära regionen för att spegla den sekundära virtuella datorns hanterade diskar.         |
|Cache Storage     | Du kan ange ett cache Storage-konto som ska användas under replikeringen. Ett nytt lagrings konto för cachen skapas som standard, om det inte redan finns.         |
|Tillgänglighetsuppsättning     |Om den virtuella datorn i den sekundära regionen är en del av en tillgänglighets uppsättning kan du välja en tillgänglighets uppsättning för den virtuella mål datorn i den primära regionen. Som standard försöker Site Recovery hitta den befintliga tillgänglighets uppsättningen i den primära regionen och använda den. Under anpassningen kan du ange en ny tillgänglighets uppsättning.         |


### <a name="what-happens-during-reprotection"></a>Vad händer under skyddet?

Som standard inträffar följande:

1. Ett lagrings konto för cachen skapas i den region där den felande virtuella datorn körs.
2. Om mål lagrings kontot (det ursprungliga lagrings kontot i den primära regionen) inte finns, skapas ett nytt. Det tilldelade lagrings konto namnet är namnet på det lagrings konto som används av den sekundära virtuella datorn, med suffixet "ASR".
3. Om den virtuella datorn använder hanterade diskar skapas replik hanterade diskar i den primära regionen för att lagra data som replikeras från diskarna för den sekundära virtuella datorn.
4. Om mål tillgänglighets uppsättningen inte finns, skapas en ny som en del av återskydds jobbet om det behövs. Om du har anpassat skydds inställningarna används den valda uppsättningen.

När du utlöser ett skydds jobb och den virtuella mål datorn finns inträffar följande:

1. Den virtuella mål sidan är avstängd om den körs.
2. Om den virtuella datorn använder hanterade diskar skapas en kopia av ursprungliga diskar med suffixet "-ASRReplica". De ursprungliga diskarna tas bort. Kopiorna-ASRReplica används för replikering.
3. Om den virtuella datorn använder ohanterade diskar, kopplas data diskarna för den virtuella mål datorn bort och används för replikering. En kopia av OS-disken skapas och kopplas till den virtuella datorn. Den ursprungliga OS-disken är frånkopplad och används för replikering.
4. Endast ändringar mellan käll disken och mål disken synkroniseras. Skillnaderna beräknas genom att jämföra både diskarna och sedan överföras. För att hitta den uppskattade tids kontrollen nedan.
5. När synkroniseringen är klar börjar delta-replikeringen och skapar en återställnings punkt i linje med replikeringsprincipen.

När du utlöser ett skydds jobb och den virtuella mål datorn och diskarna inte finns inträffar följande:
1. Om den virtuella datorn använder hanterade diskar skapas replik diskar med suffixet "-ASRReplica". Kopiorna-ASRReplica används för replikering.
2. Om den virtuella datorn använder ohanterade diskar skapas replik diskar på mål lagrings kontot.
3. Hela diskarna kopieras från den misslyckade över-regionen till den nya mål regionen.
4. När synkroniseringen är klar börjar delta-replikeringen och skapar en återställnings punkt i linje med replikeringsprincipen.

#### <a name="estimated-time--to-do-the-reprotection"></a>Beräknad tid för att göra omskydden 

I de flesta fall replikerar Azure Site Recovery inte fullständiga data till käll regionen. Nedan visas de villkor som avgör hur mycket data som skulle replikeras:

1.  Om de virtuella käll datorerna tas bort, skadas eller inte går att komma åt på grund av ett skäl som resurs gruppens ändring/ta bort, sker IR-synkroniseringen eftersom det inte finns några tillgängliga data i käll regionen som ska användas.
2.  Om de virtuella käll datorerna är tillgängliga, beräknas bara differentiella genom att jämföra diskarna och sedan överföra dem. Se tabellen nedan för att hämta den beräknade tiden 

|\* * Exempel situation * * | \* * Tids åtgång för att skydda * * |
|--- | --- |
|Käll regionen har 1 virtuell dator med 1 TB standard disk<br/>– Endast 127 GB data används och resten av disken är tom<br/>– Disktyp är standard med 60 MiB/S-genomflöde<br/>-Ingen data ändring efter redundans| Ungefärlig tid 45 minuter – 1,5 timmar<br/> – Under återskydds Site Recovery fylls kontroll summan för hela data som tar 127 GB/45 MB ~ 45 minuter<br/>– Viss omkostnader krävs för att Site Recovery automatisk skalning som är 20-30 minuter<br/>– Inga utgående avgifter |
|Käll regionen har 1 virtuell dator med 1 TB standard disk<br/>– Endast 127 GB data används och resten av disken är tom<br/>– Disktyp är standard med 60 MiB/S-genomflöde<br/>– 45 GB data ändringar efter redundans| Ungefärlig tid 1 timmar – 2 timmar<br/>– Under återskydds Site Recovery fylls kontroll summan för hela data som tar 127 GB/45 MB ~ 45 minuter<br/>– Överförings tid för att tillämpa ändringar på 45 GB som är 45 GB/45 Mbit/s ~ 17 minuter<br/>-Utgående kostnader skulle endast vara för 45 GB-data för kontroll summan|
 



## <a name="next-steps"></a>Nästa steg

När den virtuella datorn är skyddad kan du starta en redundansväxling. Redundansväxlingen stänger den virtuella datorn i den sekundära regionen och skapar och startar en virtuell dator i den primära regionen, med vissa små stillestånds tider. Vi rekommenderar att du väljer en tid i enlighet med detta och att du kör ett redundanstest innan du påbörjar en fullständig redundans till den primära platsen. [Läs mer](site-recovery-failover.md) om redundans.
