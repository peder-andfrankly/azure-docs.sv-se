---
title: Säkerhetskopiera filer och mappar – vanliga frågor
description: Behandlar vanliga frågor om säkerhets kopiering av filer och mappar med Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b66eb7bca3c9a57f6b44697aa0340cd852fc3db4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173060"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Vanliga frågor om säkerhets kopiering av filer och mappar

Den här artikeln innehåller svar på vanliga frågor Abound säkerhets kopiering av filer och mappar med Microsoft Azure Recovery Services MARS-agenten i [Azure Backup](backup-overview.md) -tjänsten.

## <a name="configure-backups"></a>Konfigurera säkerhets kopior

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Var kan jag hämta den senaste versionen av MARS-agenten?

Den senaste MARS-agenten som används för att säkerhetskopiera Windows Server-datorer, System Center DPM och Microsoft Azure Backup Server är tillgänglig för [hämtning](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Hur länge är valv referenser giltiga?

Valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Om filen med autentiseringsuppgifter upphör att gälla laddar du ned filen igen från Azure Portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Från vilka enheter kan jag säkerhetskopiera filer och mappar?

Du kan inte säkerhetskopiera följande typer av enheter och volymer:

* Flyttbart medium: alla säkerhets kopierings objekts källor måste rapporteras som fasta.
* Skrivskyddade volymer: volymen måste vara skrivbar för att VSS-tjänsten (Volume Shadow Copy Service) ska fungera.
* Frånkopplade volymer: volymen måste vara online för att VSS ska fungera.
* Nätverks resurser: volymen måste vara lokal på servern för att säkerhets kopie ras med onlinesäkerhetskopiering.
* BitLocker-skyddade volymer: volymen måste låsas upp innan säkerhets kopieringen kan utföras.
* Identifiering av filsystem: NTFS är det enda filsystem som stöds.

### <a name="what-file-and-folder-types-are-supported"></a>Vilka fil-och mapp-typer stöds?

[Läs mer](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) om de typer av filer och mappar som stöds för säkerhets kopiering.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan jag använda MARS-agenten för att säkerhetskopiera filer och mappar på en virtuell Azure-dator?  

Ja. Azure Backup tillhandahåller säkerhets kopiering på VM-nivå för virtuella Azure-datorer med hjälp av VM-tillägget för Azure VM-agenten. Om du vill säkerhetskopiera filer och mappar i gäst operativ systemet för Windows på den virtuella datorn kan du installera MARS-agenten.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan jag använda MARS-agenten för att säkerhetskopiera filer och mappar på tillfällig lagring för den virtuella Azure-datorn?

Ja. Installera MARS-agenten och säkerhetskopiera filer och mappar på gäst Windows-operativsystemet till tillfällig lagring.

* Säkerhets kopierings jobben fungerar inte när tillfälliga lagrings data rensas ut.
* Om tillfälliga lagrings data tas bort kan du bara återställa till icke-flyktig lagring.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hur gör jag för att registrera en server i en annan region?

Säkerhetskopierade data skickas till data centret för valvet där-servern är registrerad. Det enklaste sättet att ändra data centret är att avinstallera och installera om agenten och sedan registrera datorn på ett nytt valv i den region du behöver.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Stöder MARS-agenten Windows Server 2012-deduplicering?

Ja. MARS-agenten konverterar deduplicerade data till normala data när säkerhets kopieringen förbereds. Den optimerar sedan data för säkerhets kopiering, krypterar data och skickar sedan krypterade data till valvet.

## <a name="manage-backups"></a>Hantera säkerhetskopior

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Vad händer om jag byter namn på en Windows-dator som är konfigurerad för säkerhets kopiering?

När du byter namn på en Windows-dator stoppas alla aktuella konfigurerade säkerhets kopieringar.

* Du måste registrera det nya dator namnet med säkerhets kopierings valvet.
* När du registrerar det nya namnet med valvet är den första åtgärden en *fullständig* säkerhets kopia.
* Om du behöver återställa data som har säkerhetskopierats till valvet med det gamla Server namnet använder du alternativet för att återställa till en alternativ plats i guiden Återställ data. [Läs mer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Vad är den maximala fil Sök vägens längd för säkerhets kopiering?

MARS-agenten använder NTFS och använder längd specifikationen för fil Sök vägen som begränsas av [Windows-API: et](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Om de filer som du vill skydda är längre än det tillåtna värdet kan du säkerhetskopiera den överordnade mappen eller disk enheten.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Vilka tecken är tillåtna i fil Sök vägar?

MARS-agenten använder NTFS och tillåter tecken som [stöds](/windows/desktop/FileIO/naming-a-file#naming-conventions) i fil namn/sökvägar.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Varningen "Azure-säkerhetskopieringar har inte kon figurer ATS för den här servern" visas.

Den här varningen kan visas även om du har konfigurerat en säkerhets kopierings princip när inställningarna för säkerhets kopierings schema som lagras på den lokala servern inte är samma som inställningarna som lagras i säkerhets kopierings valvet.

* När servern eller inställningarna har återställts till ett känt fungerande tillstånd kan säkerhets kopierings scheman bli osynkroniserade.
* Om du får den här varningen [konfigurerar](backup-azure-manage-windows-server.md) du säkerhets kopierings principen igen och kör sedan en säkerhets kopiering på begäran för att synkronisera om den lokala servern med Azure.

## <a name="manage-the-backup-cache-folder"></a>Hantera mappen för säkerhetskopierade cache

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Vilken är den minsta nödvändiga storleken på cachelagringsmappen?

Storleken på cachelagringsmappen avgör mängden data som säkerhetskopieras.

* Cache-mappens volymer måste ha ett ledigt utrymme som motsvarar minst 5-10% av den totala storleken på säkerhets kopierings data.
* Om volymen har mindre än 5% ledigt utrymme kan du antingen öka volym storleken eller flytta cache-mappen till en volym med tillräckligt med utrymme.
* Om du säkerhetskopierar Windows-systemtillstånd behöver du ytterligare 30-35 GB ledigt utrymme på volymen som innehåller cache-mappen.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Så här kontrollerar du om mappen Scratch är giltig och tillgänglig?

1. Som standard finns en Scratch-mapp på `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Kontrol lera att sökvägen till din startmapp-plats matchar värdena i de register nyckel poster som visas nedan:

  | Sökväg i registret | Registernyckel | Värde |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Ny plats för cachemappen* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Ny plats för cachemappen* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hur gör jag för att ändra cache-platsen för MARS-agenten?

1. Kör det här kommandot i en upphöjd kommando tolk för att stoppa säkerhets kopierings motorn:

    ```Net stop obengine```

2. Om du har konfigurerat säkerhets kopiering av system tillstånd öppnar du disk hantering och avmonterar diskarna med namn i formatet `"CBSSBVol_<ID>"`.
3. Flytta inte filerna. Kopiera i stället mappen cache Space till en annan enhet som har tillräckligt med utrymme.
4. Uppdatera följande register poster med sökvägen till den nya cache-mappen.

    | Sökväg i registret | Registernyckel | Värde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Ny plats för cachemappen* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Ny plats för cachemappen* |

5. Starta om säkerhets kopierings motorn i en upphöjd kommando tolk:

  ```command
  Net stop obengine

  Net start obengine
  ```

6. Kör en säkerhets kopiering på begäran. När säkerhets kopieringen har slutförts med den nya platsen kan du ta bort den ursprungliga cache-mappen.

### <a name="where-should-the-cache-folder-be-located"></a>Var ska cache-mappen finnas?

Följande platser rekommenderas inte för cachelagringsmappen:

* Nätverks resurs/flyttbart medium: cache-mappen måste vara lokal på den server som behöver säkerhets kopie ras med onlinesäkerhetskopiering. Nätverks platser eller flyttbara medier som USB-enheter stöds inte
* Offline-volymer: cache-mappen måste vara online för förväntad säkerhets kopiering med Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Finns det några attribut i cache-mappen som inte stöds?

Följande attribut eller deras kombinationer stöds inte för cachelagringsmappen:

* Krypterade
* Deduplicerade
* Komprimerade
* Utspridda
* Referenspunkt

Cachelagringsmappen och den virtuella hårddisken för metadata har inte de attribut som krävs för Azure Backup-agenten.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Finns det något sätt att justera mängden bandbredd som används för säkerhets kopiering?

Ja, du kan använda alternativet **ändra egenskaper** i mars-agenten för att justera bandbredden och tids inställningen. [Läs mer](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Återställ

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter ett pågående återställnings jobb?

Om ett pågående återställnings jobb avbryts stoppas återställnings processen. Alla filer som återställts innan uppsägningen stanna kvar på den konfigurerade destinationen (ursprunglig eller alternativ plats), utan några återställningar.

## <a name="next-steps"></a>Nästa steg

[Lär dig](tutorial-backup-windows-server-to-azure.md) hur du säkerhetskopierar en Windows-dator.
