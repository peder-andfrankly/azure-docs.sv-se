---
title: Roller och krav för Azure Data Share
description: Lär dig mer om åtkomst kontroll roller och-krav för dataproviders och data konsumenter för att dela data i Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490573"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Roller och krav för Azure Data Share 

Den här artikeln beskriver de roller som krävs för att dela data med Azure Data Share, samt för att acceptera och ta emot data med Azure Data Share. 

## <a name="roles-and-requirements"></a>Roller och krav

Azure Data Share använder hanterade identiteter för Azure-tjänster (kallades tidigare MSIs) för att autentisera till underliggande lagrings konton för att kunna läsa data som delas av en DataProvider, samt ta emot data som delas som en data konsument. Det innebär att det inte finns något utbyte av autentiseringsuppgifter mellan data leverantören och data konsumenten. 

Hanterad tjänstidentitet måste beviljas åtkomst till det underliggande lagrings kontot eller SQL-databasen. Azure Data Share-tjänsten använder Azure Data Resource-resursens Hanterad tjänstidentitet för att läsa och skriva data. Användaren av Azure data sharing måste kunna skapa en roll tilldelning för Hanterad tjänstidentitet till lagrings kontot eller SQL-databasen som de delar data från/till. 

När det gäller lagring finns behörighet att skapa roll tilldelningar i **ägar** rollen, rollen administratör för användar åtkomst eller en anpassad roll med Microsoft. auktorisering/roll tilldelningar/Skriv behörighet tilldelad. 

Om du inte är ägare till lagrings kontot i fråga och du inte kan skapa en roll tilldelning för Azure Data Resource-resursens hanterade identitet själv kan du begära en Azure-administratör för att skapa en roll tilldelning för din räkning. 

Nedan visas en sammanfattning av de roller som har tilldelats till data resursen resurs-hanterad identitet:

| |  |  |
|---|---|---|
|**Lagrings typ**|**Data leverantörs lager**|**Mål lager för data konsument**|
|Azure Blob Storage| Storage BLOB data Reader | Storage BLOB data-deltagare
|Azure Data Lake gen1 | Ägare | Stöds inte
|Azure Data Lake Gen2 | Storage BLOB data Reader | Storage BLOB data-deltagare
|Azure SQL | dbo | dbo 
|

### <a name="data-providers"></a>Data leverantörer 
Om du vill lägga till en data uppsättning i en Azure-Dataresurs måste data leverantörs data dela resurs-hanterad identitet läggas till i rollen Storage BLOB data Reader. Detta görs automatiskt av Azure Data Share-tjänsten om användaren lägger till data uppsättningar via Azure och är ägare till lagrings kontot, eller är medlem i en anpassad roll som har tilldelats Microsoft. auktorisering/roll tilldelningar/Skriv behörighet. 

Alternativt kan användaren ha en Azure-administratör som lägger till data resursen resurs hanterad identitet i Storage BLOB data Reader-rollen manuellt. Om du skapar den här roll tilldelningen manuellt av administratören kommer den att bli annullerad att bli ägare till lagrings kontot eller ha en anpassad roll tilldelning. Detta gäller för data som delas från Azure Storage eller Azure Data Lake Gen2. 

Om du delar data från Azure Data Lake gen1, måste roll tilldelningen göras till ägar rollen. 

Följ stegen nedan om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet:

1. Navigera till lagrings kontot.
1. Välj **Access Control (IAM)** .
1. Välj **Lägg till en roll tilldelning**.
1. Under *roll*väljer du *Storage BLOB data Reader*.
1. Under *Välj*anger du namnet på ditt Azure Data resurs konto.
1. Klicka på *Spara*.

För SQL-baserade källor måste en användare skapas från en extern provider i SQL-databasen som data delas från med samma namn som Azure Data Share-kontot. Ett exempel skript tillsammans med andra krav för SQL-baserad delning finns i själv studie kursen [Dela dina data](share-your-data.md) . 

### <a name="data-consumers"></a>Data konsumenter
För att kunna ta emot data, måste data som förbrukas av resurs hanterade identiteter läggas till i rollen Storage BLOB data Contributor och/eller dbo-rollen för en SQL-databas om data tas emot i en SQL-databas. 

När det gäller lagring görs detta automatiskt av Azure Data Share-tjänsten om användaren lägger till data uppsättningar via Azure och är ägare till lagrings kontot, eller är medlem i en anpassad roll som har rollen Microsoft. auktorisering/roll tilldelningar/Skriv behörighet ATS. 

Alternativt kan användaren ha en Azure-administratör för att lägga till data resursen resurs hanterad identitet i Storage BLOB data Contributor-rollen manuellt. Om du skapar den här roll tilldelningen manuellt av administratören kommer den att bli annullerad att bli ägare till lagrings kontot eller ha en anpassad roll tilldelning. Observera att detta gäller för data som delas till Azure Storage eller Azure Data Lake Gen2. Det finns inte stöd för att ta emot data till Azure Data Lake gen1. 

Om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet manuellt följer du stegen nedan:

1. Navigera till lagrings kontot.
1. Välj **Access Control (IAM)** .
1. Välj **Lägg till en roll tilldelning**.
1. Under *roll*väljer du *Storage BLOB data Contributor*. 
1. Under *Välj*anger du namnet på ditt Azure Data resurs konto.
1. Klicka på *Spara*.

Om du delar data med hjälp av våra REST-API: er måste du skapa roll tilldelningarna manuellt genom att lägga till data resurs kontot i till lämpliga roller. 

Om du tar emot data i en SQL-baserad källa ser du till att en ny användare skapas från en extern provider med samma namn som ditt Azure Data Share-konto. Se krav i själv studie kursen [Godkänn och ta emot data](subscribe-to-data-share.md) . 

Mer information om hur du lägger till en roll tilldelning finns i [den här dokumentationen,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) som beskriver hur du lägger till en roll tilldelning i en Azure-resurs. 

## <a name="resource-provider-registration"></a>Registrering av resurs leverantör 

När du godkänner en Azure Data Share-inbjudan måste du registrera Microsoft. DataShare-resurs leverantören manuellt i din prenumeration. Följ de här stegen för att registrera Microsoft. DataShare-resurs leverantören i din Azure-prenumeration. 

1. I Azure Portal navigerar du till **prenumerationer**.
1. Välj den prenumeration som du använder för Azure Data-resursen.
1. Klicka på **resurs leverantörer**.
1. Sök efter Microsoft. DataShare.
1. Klicka på **Registrera**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om roller i Azure – [förstå roll definitioner](../role-based-access-control/role-definitions.md)

