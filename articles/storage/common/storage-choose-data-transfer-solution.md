---
title: Välj en Azure-lösning för data överföring | Microsoft Docs
description: Lär dig hur du väljer en Azure-lösning för data överföring baserat på data storlekar och tillgänglig nätverks bandbredd i din miljö
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 838d65da90ec0daef69375e5a75bcb497a0c3512
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900392"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Välj en Azure-lösning för data överföring

Den här artikeln innehåller en översikt över några vanliga Azure Data Transfer-lösningar. Artikeln länkar också till rekommenderade alternativ beroende på nätverks bandbredden i din miljö och storleken på de data som du vill överföra.

## <a name="types-of-data-movement"></a>Typer av data förflyttning

Data överföring kan vara offline eller via nätverks anslutningen. Välj din lösning beroende på din:

- **Data storlek** – storleken på de data som ska överföras.
- **Överförings frekvens** – engångs-eller periodisk data inmatning och
- **Nätverk** – bandbredd som är tillgänglig för data överföring i din miljö.

Data flytten kan vara av följande typer:

- **Offline-överföring med** leverans bara enheter – Använd fysiska speditörer om du vill göra en Mass överföring av Mass data offline. Microsoft skickar en disk eller en säker specialiserad enhet. Du kan också köpa och leverera egna diskar. Du kopierar data till enheten och skickar dem sedan till Azure där data har laddats upp.  De tillgängliga alternativen för det här fallet är Data Box Disk, Data Box-enhet, Data Box Heavy och importera/exportera (Använd dina egna diskar).

- **Nätverks överföring** – du överför dina data till Azure via din nätverks anslutning. Detta kan göras på många sätt.

    - **Grafiskt gränssnitt** – om du ibland överför bara några få filer och inte behöver automatisera data överföringen kan du välja ett grafiskt gränssnitts verktyg som Azure Storage Explorer eller ett webbaserat verktyg för granskning i Azure Portal.
    - **Skriptad eller programmerings överföring** – du kan använda optimerade program varu verktyg som vi tillhandahåller eller anropar våra REST-API: er/SDK: er direkt. Tillgängliga skript bara verktyg är AzCopy, Azure PowerShell och Azure CLI. För programmerings gränssnitt använder du en av SDK: erna för .NET, Java, python, Node/ C++js,, go, php eller ruby.
    - **Lokala enheter** – vi tillhandahåller en fysisk eller virtuell enhet som finns i ditt data Center och optimerar data överföringen över nätverket. Dessa enheter tillhandahåller också en lokal cache med filer som används ofta. Den fysiska enheten är Data Box Edge och den virtuella enheten är Data Box Gateway. Både körs permanent i dina lokaler och ansluta till Azure via nätverket.
    - **Pipeline för hanterade data** – du kan konfigurera en moln pipeline för att regelbundet överföra filer mellan flera Azure-tjänster, lokalt eller en kombination av två. Använd Azure Data Factory för att konfigurera och hantera datapipelines och flytta och transformera data för analys.

Följande visuella information visar rikt linjerna för att välja olika Azure Data Transfer-verktyg beroende på nätverks bandbredden som är tillgänglig för överföring, data storlek som är avsedd för överföring och överförings frekvensen.

![Azure Data Transfer tools](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**De övre gränserna för offline-överförings enheter – Data Box Disk, Data Box-enhet och Data Box Heavy kan utökas genom att placera flera order av en enhets typ.*

## <a name="selecting-a-data-transfer-solution"></a>Välja en data överförings lösning

Besvara följande frågor för att hjälpa dig att välja en data överförings lösning:

- Är din tillgängliga nätverks bandbredd begränsad eller obefintlig, och du vill överföra stora data uppsättningar?
  
    Om ja, se: [Scenario 1: Överför stora data mängder utan eller låg nätverks bandbredd](storage-solution-large-dataset-low-network.md).
- Vill du överföra stora data uppsättningar över nätverket och du har en måttlig till hög bandbredd i nätverket?

    Om ja, se: [Scenario 2: Överför stora data uppsättningar med måttlig till hög nätverks bandbredd](storage-solution-large-dataset-moderate-high-network.md).
- Vill du ibland bara överföra några få filer över nätverket?

    Om ja, se [scenario 3: Överför små data uppsättningar med begränsad till måttlig nätverks bandbredd](storage-solution-small-dataset-low-moderate-network.md).
- Letar du efter data överföring vid bestämda tidpunkter med jämna mellanrum?

    Om ja, Använd de skript/programmerings alternativ som beskrivs i [scenario 4: Periodiska data](storage-solution-periodic-data-transfer.md)överföringar.
- Letar du efter kontinuerlig data överföring på väg?

    Om ja, Använd alternativen i [scenario 4: Periodiska data](storage-solution-periodic-data-transfer.md)överföringar.
 

## <a name="data-transfer-feature-in-azure-portal"></a>Data överförings funktion i Azure Portal

Du kan också gå till ditt Azure Storage-konto i Azure Portal och välja funktionen för **data överföring** . Ange Nätverks bandbredden i din miljö, storleken på de data som du vill överföra och frekvensen för data överföring. Du ser de optimala lösningar för data överföring som motsvarar den information som du har angett. 

## <a name="next-steps"></a>Nästa steg

- [Få en introduktion till Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Läs en översikt över AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Använda Azure PowerShell med Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Använd Azure CLI med Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Lär dig mer om:

    - [Azure Data Box, Azure Data Box disk och Azure Data Box Heavy för offline-överföringar](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box gateway och Azure Data Box Edge för online-överföringar](https://docs.microsoft.com/azure/databox-online/).
- [Lär dig vad som är Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Använd REST-API: er för att överföra data

    - [I .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [I Java](https://docs.microsoft.com/java/api/overview/azure/storage)
