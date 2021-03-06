---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 577ecad169c30da77a69e17b247195088949ec66
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74701072"
---
I följande tabell beskrivs standard gränserna för Azures allmänna-syfte v1-, v2-och Blob Storage-konton. Den *inkommande* gränsen refererar till alla data från begär Anden som skickas till ett lagrings konto. *Utgående* gräns avser alla data från svar som tas emot från ett lagrings konto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagrings konton per region per prenumeration, inklusive både standard-och Premium konton | 250 |
| Maximal kapacitet för lagrings konto | 2 PiB för USA och Europa och 500 TiB för alla andra regioner (inklusive Storbritannien)<sup>1</sup>|
| Maximalt antal BLOB-behållare, blobbar, fil resurser, tabeller, köer, entiteter eller meddelanden per lagrings konto | Obegränsat |
| Högsta begär ande frekvens<sup>1</sup> per lagrings konto | 20 000 begär Anden per sekund |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (USA, Europa regioner) | 25 Gbit/s |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (andra regioner än USA och Europa) | 5 Gbit/s om RA-GRS/GRS är aktiverat, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgånget för General-Purpose v2-och Blob Storage-konton (alla regioner) | 50 Gbit/s |
| Högsta utgående trafik för generella v1-lagrings konton (amerikanska regioner) | 20 Gbit/s om RA-GRS/GRS är aktiverat, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för allmänna v1-lagrings konton (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS är aktiverat, 15 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal virtuella nätverks regler per lagrings konto | 200 |
| Maximalt antal IP-adress regler per lagrings konto | 200 |

<sup>1</sup> Azure standard Storage-konton stöder högre kapacitets gränser och högre gränser för ingångar efter begäran. Kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/)om du vill begära en ökning av konto gränserna för ingress. Mer information finns i avsnittet om att [presentera större och högre skalnings lagrings konton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> om du har aktiverat Read-Access (RA-GRS/ra-GZRS) är utgångs målen för den sekundära platsen identiska med de på den primära platsen. [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativen för replikering inkluderar:  
[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> Vi rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Azure Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om Azure Storage-konton finns i [Översikt över lagrings konto](../articles/storage/common/storage-account-overview.md).

Om ditt programs behov överskrider skalbarhets målen för ett enda lagrings konto kan du bygga ditt program för att använda flera lagrings konton. Du kan sedan partitionera dina data objekt över dessa lagrings konton. Mer information om volym priser finns i [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/).

Alla lagrings konton körs på en planad nätverkstopologi och stöder de skalbarhets-och prestanda mål som beskrivs i den här artikeln, oavsett när de skapades. Mer information om Azure Storage platt nätverks arkitektur och om skalbarhet finns i [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

