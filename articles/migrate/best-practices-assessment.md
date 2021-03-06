---
title: Rekommenderade metoder för utvärdering i Azure Migrate Server-utvärdering
description: Tips för att skapa utvärderingar med Azure Migrate Server-utvärdering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185985"
---
# <a name="best-practices-for-creating-assessments"></a>Metod tips för att skapa utvärderingar

[Azure Migrate](migrate-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).

I den här artikeln sammanfattas metod tips när du skapar utvärderingar med verktyget Azure Migrate Server Assessment.

## <a name="about-assessments"></a>Om utvärderingar

Utvärderingar som du skapar med Azure Migrate Server utvärdering är en tidpunkts ögonblicks bild av data. Det finns två typer av utvärderingar i Azure Migrate.

**Bedömnings typ** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar som gör rekommendationer baserat på insamlade prestanda data | Rekommendationen för VM-storlek baseras på processor-och minnes användnings data.<br/><br/> Disk typs rekommendation (standard HDD/SSD eller Premium-Managed Disks) baseras på IOPS och data flödet för lokala diskar.
**Som lokal** | Utvärderingar som inte använder prestanda data för att göra rekommendationer. | Rekommendationen för VM-storlek baseras på den lokala virtuella dator storleken<br/><br> Den rekommenderade disk typen baseras på vad du väljer i inställningen lagrings typ för utvärderingen.

### <a name="example"></a>Exempel
Om du t. ex. har en lokal virtuell dator med fyra kärnor med 20% användning och minne på 8 GB med 10% användning, så är utvärderingen följande:

- **Prestanda baserad utvärdering**:
    - Identifierar effektiva kärnor och minne baserat på kärnan (4 x 0,20 = 0,8) och minne (8 GB x 0,10 = 0,8) användning.
    - Använder den bekvämlighets faktor som anges i bedömnings egenskaper (vi antar 1,3 x) för att hämta de värden som ska användas för storleks ändring. 
    - Rekommenderar den närmaste virtuella dator storleken i Azure som har stöd för ~ 1,04 kärnor (0,8 x 1,3) och ~ 1,04 GB (0,8 x 1,3) minne.

- **Som är (som lokal) utvärdering**:
    -  Rekommenderar en virtuell dator med fyra kärnor; 8 GB minne.

## <a name="best-practices-for-creating-assessments"></a>Metod tips för att skapa utvärderingar

Azure Migrates apparaten profiler använder kontinuerligt din lokala miljö och skickar metadata-och prestanda data till Azure. Följ dessa rekommenderade metoder för utvärdering av servrar som identifierats med en installation:

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina datorer visas i Azure Migrate-portalen.
- **Skapa prestanda baserad utvärdering**: när du har konfigurerat identifiering rekommenderar vi att du väntar minst en dag innan du kör en prestanda-baserad utvärdering:
    - Insamling av prestanda data tar tid. Om du väntar minst en dag säkerställer det att det finns tillräckligt med prestanda data punkter innan du kör utvärderingen.
    - När du kör prestandabaserade utvärderingar kontrollerar du att du profilerar din miljö för utvärderings perioden. Om du till exempel skapar en utvärdering med en varaktighet på en vecka måste du vänta i minst en vecka efter att du har startat identifieringen för att alla data punkter ska samlas in. Om du inte gör det får utvärderingen inget omdöme på fem stjärnor.
- **Beräkna om utvärderingar**: eftersom utvärderingarna är tidpunkter för ögonblicks bilder uppdateras de inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med senaste data måste du beräkna om den.

Följ dessa metod tips för utvärdering av servrar som importer ATS till Azure Migrate via. CSV-fil:

- **Skapa som-är-utvärderingar**: du kan skapa som-är-utvärderingar direkt när dina datorer visas i Azure Migrate-portalen.
- **Skapa prestandabaserade utvärderingar**: Detta hjälper till att få en bättre kostnads uppskattning, särskilt om du har överetablerat Server kapacitet lokalt. Noggrannheten hos den prestandabaserade utvärderingen beror dock på de prestanda data som du har angett för servrarna. 
- **Beräkna om utvärderingar**: eftersom utvärderingarna är tidpunkter för ögonblicks bilder uppdateras de inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med de senaste importerade data måste du beräkna om den.

## <a name="best-practices-for-confidence-ratings"></a>Metod tips för förtroende värderingar

När du kör prestandabaserade utvärderingar tilldelas en förtroende nivå från 1 – stjärna (lägsta) till 5 stjärnor (högsta) till utvärderingen. Så här använder du Tillförlitlighets klassificeringen effektivt:
- Azure Migrate Server utvärderingen behöver användnings data för CPU/minne för virtuell dator.
- För varje disk som är ansluten till den lokala virtuella datorn behöver den läsa/skriva IOPS/data flödes data.
- För varje nätverkskort som är kopplat till den virtuella datorn behöver det nätverket i/ut-data.

Beroende på hur många data punkter i procent som är tillgängliga för den valda varaktigheten, anges förtroendet för en utvärdering som sammanfattas i följande tabell.

   **Tillgänglighet för data punkt** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor


## <a name="common-assessment-issues"></a>Vanliga utvärderings problem

Så här löser du några vanliga miljö problem som påverkar utvärderingen.

###  <a name="out-of-sync-assessments"></a>Utvärderingar utanför synkroniseringen

Om du lägger till eller tar bort datorer från en grupp efter att du har skapat en utvärdering, markeras den utvärdering som du har skapat **utanför synkroniseringen**. Kör utvärderingen igen (**Beräkna om**) för att återspegla grupp ändringarna.

### <a name="outdated-assessments"></a>Inaktuella bedömningar

Om det finns lokala ändringar av virtuella datorer som finns i en grupp som har bedömts, markeras utvärderingen som **föråldrad**. Om du vill visa ändringarna kör du utvärderingen igen.

### <a name="low-confidence-rating"></a>Bedömning av låg exakthet

En utvärdering kanske inte har alla data punkter av olika anledningar:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en *prestanda baserad utvärdering* med varaktigheten en vecka, måste du vänta i minst en vecka efter att du har startat identifieringen av alla data punkter som ska samlas in. Du kan alltid klicka på **Beräkna** om för att se den senaste giltiga förtroende klassificeringen. Förtroende klassificeringen gäller endast när du skapar en *prestanda baserad* utvärdering.

- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer var avstängda under en viss period, kan Server Assessment inte samla in prestandadata för den perioden.

- Få virtuella datorer skapades efter att identifieringen startades i Server Assessment. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I detta fall kommer prestandadata för de nya virtuella datorerna inte att vara tillgängliga för hela tidsperioden och säkerhetsomdömet blir lågt.


## <a name="next-steps"></a>Nästa steg

- [Lär dig](concepts-assessment-calculation.md) hur utvärderingar beräknas.
- [Lär dig](how-to-modify-assessment.md) hur du anpassar en utvärdering.
