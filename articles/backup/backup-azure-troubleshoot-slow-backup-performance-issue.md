---
title: Felsöka långsam säkerhets kopiering av filer och mappar
description: Innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till Azure Backup prestanda problem
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 2b7b8903da0d8dd83591b260bacb496b0c253ae3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172589"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup

Den här artikeln innehåller fel söknings vägledning som hjälper dig att diagnostisera orsaken till långsamma säkerhets kopierings prestanda för filer och mappar när du använder Azure Backup. När du använder Azure Backup Agent för att säkerhetskopiera filer kan säkerhets kopieringen ta längre tid än förväntat. Den här fördröjningen kan ha orsakats av ett eller flera av följande:

* [Det finns prestanda Flask halsar på datorn som säkerhets kopie ras.](#cause1)
* [En annan process eller ett antivirus program stör Azure Backup processen.](#cause2)
* [Säkerhets kopierings agenten körs på en virtuell Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal filer.](#cause4)

Innan du börjar felsöka problem rekommenderar vi att du hämtar och installerar den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar av säkerhets kopierings agenten för att åtgärda olika problem, lägga till funktioner och förbättra prestandan.

Vi rekommenderar också starkt att du läser igenom [vanliga frågor om Azure Backup-tjänsten](backup-azure-backup-faq.md) för att se till att du inte har några vanliga konfigurations problem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: prestanda Flask halsar på datorn

Flask halsar på datorn som säkerhets kopie ras kan orsaka fördröjningar. Till exempel kan datorns möjlighet att läsa eller skriva till disk, eller tillgänglig bandbredd för att skicka data via nätverket, orsaka Flask halsar.

Windows innehåller ett inbyggt verktyg som kallas [prestanda övervakaren](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (PerfMon) för att identifiera Flask halsarna.

Här följer några prestanda räknare och intervall som kan vara till hjälp vid diagnostisering av flask halsar för optimala säkerhets kopieringar.

| Medelvärde | Status |
| --- | --- |
| Logisk disk (fysisk disk)--% inaktiv |• 100% inaktiv till 50% Idle = felfri</br>• 49% inaktiv till 20% inaktiv = varning eller Övervakare</br>• 19% inaktiv till 0% inaktiv = kritisk eller ur specifikation |
| Logisk disk (fysisk disk)--% medel s disk läsning eller skrivning |• 0,001 MS till 0,015 MS = felfri</br>• 0,015 MS till 0,025 MS = varning eller Övervakare</br>• 0,026 MS eller längre = kritisk eller ur specifikationen |
| Logisk disk (fysisk disk)--Aktuell diskkölängd (för alla instanser) |80 förfrågningar i mer än 6 minuter |
| Minne – icke växlings Bart system minne-byte |• Mindre än 60% av poolen förbrukade = felfri<br>• 61% till 80% av poolen förbrukat = varning eller övervaka</br>• Större än 80% pool förbrukad = kritisk eller från specifikation |
| Minne--växlings Bart system minne-byte |• Mindre än 60% av poolen förbrukade = felfri</br>• 61% till 80% av poolen förbrukat = varning eller övervaka</br>• Större än 80% pool förbrukad = kritisk eller från specifikation |
| Minne – tillgängliga megabyte |• 50% ledigt minne tillgängligt eller mer = felfri</br>• 25% ledigt minne tillgängligt = övervaka</br>• 10% ledigt minne tillgängligt = varning</br>• Mindre än 100 MB eller 5% ledigt minne tillgängligt = kritiskt eller ur specifikationen |
| Processor –\%processor tid (alla instanser) |• Mindre än 60% förbrukat = felfri</br>• 61% till 90% förbrukat = övervaka eller varning</br>• 91% till 100% förbrukat = kritisk |

> [!NOTE]
> Om du fastställer att infrastrukturen är orsaken rekommenderar vi att du defragmenterar diskarna regelbundet för bättre prestanda.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: en annan process eller ett antivirus program som stör Azure Backup

Vi har sett flera instanser där andra processer i Windows-systemet har negativt prestanda för Azure Backup Agent processen. Om du till exempel använder både Azure Backup-agenten och ett annat program för att säkerhetskopiera data, eller om antivirus programmet körs och har låst filer som ska säkerhets kopie ras, kan flera lås på filer orsaka konkurrens. I den här situationen kan säkerhets kopieringen Miss Miss kan, eller så kan det ta längre tid än förväntat.

Den bästa rekommendationen i det här scenariot är att stänga av det andra säkerhets kopierings programmet för att se om säkerhets kopierings tiden för Azure Backup agenten ändras. Att se till att flera säkerhets kopierings jobb inte körs samtidigt är tillräckligt för att förhindra att de påverkar varandra.

För antivirus program rekommenderar vi att du undantar följande filer och platser:

* C:\Program\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program\Microsoft Azure Recovery Services agent \ mappar
* Arbets plats (om du inte använder standard platsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: säkerhets kopierings agenten körs på en virtuell Azure-dator

Om du kör säkerhets kopierings agenten på en virtuell dator blir prestandan långsammare än när du kör den på en fysisk dator. Detta förväntas på grund av IOPS-begränsningar.  Du kan dock optimera prestanda genom att växla data enheter som säkerhets kopie ras till Azure Premium Storage. Vi arbetar med att åtgärda det här problemet och korrigeringen är tillgänglig i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: säkerhetskopiera ett stort antal filer

Det tar längre tid att flytta en stor mängd data än att flytta en mindre data volym. I vissa fall är säkerhets kopierings tiden relaterad till inte bara storleken på data, utan också antalet filer eller mappar. Detta gäller särskilt när miljon tals små filer (några byte till några kilobyte) säkerhets kopie ras.

Det här problemet beror på att när du säkerhetskopierar data och flyttar dem till Azure, katalogiserar Azure samtidigt dina filer. I vissa sällsynta fall kan katalog åtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå Flask halsen och därefter arbeta på nästa steg:

* **UI visar förloppet för data överföringen**. Data överförs fortfarande. Nätverks bandbredden eller data storleken kan orsaka fördröjningar.
* **Användar gränssnittet visar inte förloppet för data överföringen**. Öppna loggfilerna som finns i C:\Program\Microsoft Azure Recovery Services Agent\Temp och kontrol lera sedan posten FileProvider:: EndData i loggarna. Den här posten indikerar att data överföringen är slutförd och att katalog åtgärden sker. Avbryt inte säkerhets kopierings jobben. Vänta i stället lite längre tills katalog åtgärden har slutförts. Kontakta [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support)om problemet kvarstår.
