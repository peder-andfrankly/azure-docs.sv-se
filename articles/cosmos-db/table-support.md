---
title: Azure Table Storage-stöd i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB Tabell-API och Azure Storage tabeller fungerar tillsammans genom att dela samma tabell data modell med åtgärder
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 4f742ce34ec429f0fc822562c1ad4ad07f3e7965
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870436"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Utveckla med Azure Cosmos DB Table API:t och Azure Table Storage

Azure Cosmos DB Table API:t och Azure Table Storage delar samma tabelldatamodell och exponerar samma åtgärder för att skapa, ta bort, uppdatera och fråga via sina SDK:er.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Utveckla med Azure Cosmos DB Table API:t

Just nu har [Azure Cosmos DB Table API:t](table-introduction.md) fyra SDK:er tillgängliga för utveckling: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Det här biblioteket har NET Standard som mål och har samma klasser och metodsignaturer som den offentliga versionen av [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), men har även funktioner för att ansluta till Azure Cosmos DB-konton via Tabell-API. Användare av .NET Framework Library [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) rekommenderas för att uppgradera till [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) eftersom det är i underhålls läge och kommer att bli inaktuellt snart.

* [Python SDK](table-sdk-python.md): den nya Azure Cosmos DB python SDK är den enda SDK som stöder Azure Table Storage i python. Detta SDK fungerar med både Azure Table Storage och Azure Cosmos DB Table API:t.

* [Java SDK](table-sdk-java.md): den här Azure Storage SDK kan ansluta till Azure Cosmos DB-konton med hjälp av tabell-API.

* [Node. js SDK](table-sdk-nodejs.md): den här Azure Storage SDK kan ansluta till Azure Cosmos DB-konton med hjälp av tabell-API.


Ytterligare information om hur du arbetar med Table API:t finns i artikeln [Vanliga frågor och svar: utveckla med Table API](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Utveckla med Azure Table Storage

Azure Table storage har de här SDK:erna tillgängliga för utveckling:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Det här biblioteket låter dig arbeta med lagringstabelltjänsten.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Azure Cosmos DB Table SDK för python stöder tjänsten Table Storage (eftersom Azure Table Storage och Cosmos DB är Tabell-API dela samma funktioner och funktioner, och i en ansträngning för att factorize vår SDK-utvecklings ansträngningar rekommenderar vi att du använder denna SDK).
- [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java). Den här Azure Storage SDK:n tillhandahåller ett klientbibliotek i Java för att använda Azure Table-lagring.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Detta SDK erbjuder ett Node.js-paket och ett webbläsarkompatibelt JavaScript-klientbiblioteket för att använda lagringstabelltjänsten.
- [AzureRmStorageTable PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRmStorageTable). Den här PowerShell-modulen har cmdletar för att arbeta med lagringstabeller.
- [Azure Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp/). Det här biblioteket låter dig skapa program mot Azure Storage.
- [Azure Storage Table-klientbiblioteket för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Det här projektet tillhandahåller ett Ruby-paket som gör det enkelt att komma åt Azure Table-lagringstjänster.
- [Azure Storage Table PHP-klientbibliotek](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Det här projektet tillhandahåller ett PHP-klientbibliotek som gör det enkelt att komma åt Azure Table-lagringstjänster.


   





