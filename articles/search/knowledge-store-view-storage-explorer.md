---
title: Visa ett kunskaps lager (för hands version) med Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visa och analysera Azure Kognitiv sökning kunskaps lager med Azure Portals Storage Explorer. Kunskaps lagret är för närvarande en offentlig för hands version.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2df05cf20ef51b2d5ca866f22bd9450dd6acaf
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406559"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visa ett kunskaps lager med Storage Explorer

> [!IMPORTANT] 
> Kunskaps lagret är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande begränsad Portal support och inget stöd för .NET SDK.

I den här artikeln lär du dig hur du kan ansluta till och utforska ett kunskaps lager med hjälp av Storage Explorer i Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

+ Följ stegen i [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md) eller [skapa ett Azure kognitiv sökning kunskaps lager genom att använda rest](knowledge-store-create-rest.md) för att skapa det exempel kunskaps arkiv som användes i den här genom gången.

+ Du kommer också att behöva namnet på det Azure Storage-konto som du använde för att skapa kunskaps lagret, tillsammans med dess åtkomst nyckel från Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visa, redigera och fråga ett kunskaps lager i Storage Explorer

1. [Öppna det lagrings konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) som du använde för att skapa kunskaps lagret i Azure Portal.

1. I lagrings kontots vänstra navigerings fönster klickar du på **Storage Explorer**.

1. Expandera listan **tabeller** om du vill visa en lista över Azure Table-projektioner som skapades när du körde guiden **Importera data** på din hotell gransknings exempel data.

Välj en tabell för att visa de utförliga data, inklusive nyckel fraser sentiment poäng, latitud data och longitud.

   ![Visa tabeller i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visa tabeller i Storage Explorer")

Om du vill ändra data typen för ett tabell värde eller ändra enskilda värden i tabellen, klickar du på **Redigera**. När du ändrar data typen för en kolumn i en tabell rad, kommer den att tillämpas på alla rader.

   ![Redigera tabell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Redigera tabell i Storage Explorer")

Om du vill köra frågor klickar du på **fråga** i kommando fältet och anger dina villkor.  

   ![Fråg Bell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Fråg Bell i Storage Explorer")

## <a name="clean-up"></a>Rensa

När du arbetar med din egen prenumeration är det en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Anslut det här kunskaps lagret till Power BI för djupare analys eller flytta framåt med kod med hjälp av REST API och Postman för att skapa ett annat kunskaps lager.

> [!div class="nextstepaction"]
> [Ansluta till Power BI](knowledge-store-connect-power-bi.md)
> [skapa ett kunskaps lager i rest](knowledge-store-howto.md)
