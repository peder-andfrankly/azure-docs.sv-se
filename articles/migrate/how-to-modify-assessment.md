---
title: Anpassa utvärderingar för Azure Migrate Server utvärdering | Microsoft Docs
description: Beskriver hur du anpassar utvärderingar som skapats med Azure Migrate Server bedömning
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234276"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

Den här artikeln beskriver hur du anpassar utvärderingar som skapats av Azure Migrate Server-utvärdering.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Du kan använda verktyget för Azure Migrate Server bedömning för att skapa utvärderingar för lokala virtuella VMware-datorer och virtuella Hyper-V-datorer, som förberedelse för migrering till Azure.

## <a name="about-assessments"></a>Om utvärderingar

Det finns två typer av utvärderingar som du kan köra med hjälp av Azure Migrate Server bedömning.

**Beskrivningar** | **Detaljer** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: Baserat på CPU-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : Baserat på IOPS och data flöde för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: Baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disk typ**: Baserat på inställningen för lagrings typ som du väljer för utvärderingen.


## <a name="how-is-an-assessment-done"></a>Hur görs en utvärdering?

En utvärdering som gjorts i Azure Migrate Server-utvärderingen har tre steg. Utvärderingen börjar med en analys av lämplighet, följt av storlek och slutligen en uppskattning av månatlig kostnad. En dator flyttas bara till ett senare steg om den tidigare har passerat. Om en dator till exempel inte uppfyller Azures lämpligaste kontroll, är den markerad som olämplig för Azure, och storleks sortering och kostnads anpassning görs inte. [Läs mer.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/> Server utvärderingen stöder för närvarande dessa mål regioner: Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra 2; USA, Tyskland, centrala, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, Nord Korea Centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, US Gov, Arizona, US Gov, Texas, US Gov, Virginia, västra centrala USA, Västeuropa, västra Indien, västra USA och västra 2; USA.
**Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar du vill flytta till i Azure.<br/><br/> För lokal storleks ändring kan du ange mål lagrings typ antingen som Premium-hanterade diskar, Standard SSD-hanterade diskar eller Standard HDD-hanterade diskar. För prestandabaserade storleks ändringar kan du ange mål disk typen antingen som automatiskt, Premium-hanterade diskar, Standard HDD-hanterade diskar eller Standard SSD-hanterade diskar.<br/><br/> När du anger lagrings typen som automatisk, utförs disk rekommendationer baserat på diskens prestanda data (IOPS och data flöde). Om du anger lagrings typ som Premium/standard, rekommenderar utvärderingen en disk-SKU i den valda lagrings typen. Om du vill uppnå ett service avtal för en enda instans av virtuell dator på 99,9%, kan du ange lagrings typ som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Azure
**Reserverade instanser (RI)** | Med den här egenskapen kan du ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure och kostnads uppskattningar i utvärderingen sedan görs i RI-rabatter. Reserverade instanser stöds för närvarande bara för erbjudandet betala per användning i Azure Migrate.
**Ändra storlek på kriterium** | Det kriterium som ska användas för att ändra storlek på virtuella datorer i Azure. Du kan antingen göra *prestandabaserade* storleks ändringar eller ändra storlek på de virtuella datorerna *lokalt*, utan att behöva beakta prestanda historiken.
**Prestandahistorik** | Varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller endast när storleks kriteriet är *prestanda-baserat*.
**Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller endast när storlek är *prestanda beroende*.
**VM-serie** |     Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktions miljö som du inte planerar att migrera till virtuella datorer i A-serien i Azure, kan du undanta en-serien från listan eller serien och den högra storleks ändringen görs bara i den valda serien.
**Komfortfaktor** | Azure Migrate Server-utvärderingen anses vara en buffert (bekvämlighets faktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan.
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte kommer att köras dygnet runt i Azure, kan du ange varaktigheten (antalet dagar per månad och antalet timmar per dag) som de ska köras och kostnads uppskattningarna skulle utföras i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. Standardvärdet är Ja.


## <a name="edit-assessment-properties"></a>Redigera bedömnings egenskaper

Gör följande om du vill redigera bedömnings egenskaper när du har skapat en utvärdering:

1. Klicka på **servrar**i Azure Migrate-projektet.
2. I **Azure Migrate: Server utvärdering**, klicka på beräknings antalet.
3. I **utvärderingen**klickar du på relevant utvärdering > **Redigera egenskaper**.
5. Anpassa bedömnings egenskaperna enligt tabellen ovan.
6. Klicka på **Spara** för att uppdatera utvärderingen.


Du kan också redigera bedömnings egenskaper när du skapar en utvärdering.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
