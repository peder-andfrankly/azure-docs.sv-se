---
title: Reparera ett import jobb för Azure import/export-v1 | Microsoft Docs
description: Lär dig hur du reparerar ett import jobb som har skapats och körs med tjänsten Azure import/export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973891"
---
# <a name="repairing-an-import-job"></a>Reparera ett importjobb
Microsoft Azure Import/Exports tjänsten kan inte kopiera några av dina filer eller delar av en fil till Windows Azure-Blob Service. Några orsaker till detta är:  
  
-   Skadade filer  
  
-   Skadade enheter  
  
-   Lagrings konto nyckeln ändrades när filen överfördes.  
  
Du kan köra verktyget Microsoft Azure Import/Export med import jobbets kopiera loggfiler, och verktyget överför de saknade filerna (eller delar av en fil) till ditt Windows Azure Storage-konto för att slutföra import jobbet.  
  
## <a name="repairimport-parameters"></a>RepairImport-parametrar

Följande parametrar kan anges med **RepairImport**: 
  
|||  
|-|-|  
|**/r:** < RepairFile\>|**Obligatoriskt.** Sökväg till reparations filen som spårar förloppet för reparationen och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en och bara en reparations fil. När du startar en reparation för en specifik enhet måste du skicka sökvägen till en reparations fil som ännu inte finns. Om du vill återuppta en avbruten reparation bör du skicka namnet på en befintlig reparations fil. Den reparations fil som motsvarar mål enheten måste alltid anges.|  
|**/logdir:** < LogDirectory\>|**Valfritt.** Logg katalogen. Utförliga loggfiler skrivs till den här katalogen. Om ingen logg katalog anges används den aktuella katalogen som logg katalog.|  
|**/d:** < TargetDirectories\>|**Obligatoriskt.** En eller flera semikolonavgränsade kataloger som innehåller de ursprungliga filerna som importerades. Import enheten kan även användas, men det krävs inte om alternativa platser för originalfiler är tillgängliga.|  
|**/bk:** <BitLockerKey\>|**Valfritt.** Du bör ange BitLocker-nyckeln om du vill att verktyget ska låsa upp en krypterad enhet där de ursprungliga filerna är tillgängliga.|  
|**/SN:** < StorageAccountName\>|**Obligatoriskt.** Namnet på lagrings kontot för import jobbet.|  
|**/sk:** <StorageAccountKey\>|**Krävs** om och endast om en behållar-säkerhetsassociationer inte har angetts. Konto nyckeln för lagrings kontot för import jobbet.|  
|**/CSAS:** < behållare\>|**Krävs** om och endast om lagrings konto nyckeln inte har angetts. Behållar-SAS för åtkomst till de blobar som är associerade med import jobbet.|  
|**/CopyLogFile:** < DriveCopyLogFile\>|**Obligatoriskt.** Sökväg till logg filen för enhets kopian (antingen utförlig logg eller fel logg). Filen genereras av tjänsten Windows Azure import/export och kan laddas ned från blob-lagringen som är kopplad till jobbet. Kopierings logg filen innehåller information om misslyckade blobbar eller filer som ska repare ras.|  
|**/PathMapFile:** < DrivePathMapFile\>|**Valfritt.** Sökväg till en textfil som kan användas för att lösa tvetydigheter om du har flera filer med samma namn som du importerade i samma jobb. Första gången verktyget körs kan du fylla i den här filen med alla tvetydiga namn. Efterföljande körningar av verktyget använder den här filen för att lösa tvetydighet.|  
  
## <a name="using-the-repairimport-command"></a>Använda kommandot RepairImport  
Om du vill reparera import data genom att strömma data via nätverket måste du ange de kataloger som innehåller de ursprungliga filerna som du importerade med hjälp av parametern `/d`. Du måste också ange kopierings logg filen som du laddade ned från ditt lagrings konto. En vanlig kommando rad för att reparera ett import jobb med partiella problem ser ut så här:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
I följande exempel i en kopierings logg fil var filen 1 64-K i en fil skadad på den enhet som har levererats för import jobbet. Eftersom det här är det enda felet som anges har resten av blobarna i jobbet importer ATS.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
När den här kopierings loggen skickas till verktyget Azure import/export försöker verktyget att slutföra importen av den här filen genom att kopiera det innehåll som saknas i nätverket. Efter exemplet ovan söker verktyget efter den ursprungliga filen `\animals\koala.jpg` i de två katalogerna `C:\Users\bob\Pictures` och `X:\BobBackup\photos`. Om filen `C:\Users\bob\Pictures\animals\koala.jpg` finns kopierar verktyget Azure import/export det saknade data området till motsvarande BLOB-`http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Lösa konflikter när du använder RepairImport  
I vissa situationer kanske verktyget inte kan hitta eller öppna den nödvändiga filen av något av följande skäl: det gick inte att hitta filen, filen är inte tillgänglig, fil namnet är tvetydigt eller så är filens innehåll inte längre korrekt.  
  
Ett tvetydigt fel kan uppstå om verktyget försöker hitta `\animals\koala.jpg` och det finns en fil med det namnet under både `C:\Users\bob\pictures` och `X:\BobBackup\photos`. Det vill säga att både `C:\Users\bob\pictures\animals\koala.jpg` och `X:\BobBackup\photos\animals\koala.jpg` finns på import jobb enheterna.  
  
Med alternativet `/PathMapFile` kan du lösa de här felen. Du kan ange namnet på filen som innehåller en lista över filer som verktyget inte kunde identifiera korrekt. Följande kommando rads exempel fyller i `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Verktyget skriver sedan de problematiska fil Sök vägarna till `9WM35C2V_pathmap.txt`, en på varje rad. Till exempel kan filen innehålla följande poster när du har kört kommandot:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 För varje fil i listan, bör du försöka hitta och öppna filen för att se till att den är tillgänglig för verktyget. Om du vill instruera verktyget att hitta en fil, kan du ändra Sök vägs mappnings filen och lägga till sökvägen till varje fil på samma rad, avgränsade med ett tabbtecken:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
När du har gjort de nödvändiga filerna tillgängliga för verktyget eller uppdaterat Sök vägs mappnings filen kan du köra verktyget igen för att slutföra importen.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget Azure import/export](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
