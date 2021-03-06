---
title: Data inmatning för Azure HPC-cache – msrsync
description: Använda msrsync för att flytta data till ett Blob Storage-mål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168422"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache-data inmatning – msrsync-metod

Den här artikeln innehåller detaljerade anvisningar för hur du använder ``msrsync``-verktyget för att kopiera data till en Azure Blob Storage-behållare för användning med Azure HPC-cache.

Läs mer om hur du flyttar data till Blob Storage för Azure HPC cache genom [att läsa flytta data till Azure Blob Storage](hpc-cache-ingest.md).

``msrsync``-verktyget kan användas för att flytta data till ett Server dels lagrings mål för Azure HPC-cachen. Det här verktyget är utformat för att optimera bandbredds användningen genom att köra flera parallella ``rsync`` processer. Den är tillgänglig från GitHub på https://github.com/jbd/msrsync.

``msrsync`` delar upp käll katalogen i separata "buckets" och kör sedan enskilda ``rsync`` processer i varje Bucket.

Preliminär testning med en virtuell dator med fyra kärnor visade bäst effektivitet vid användning av 64-processer. Använd ``msrsync`` alternativet ``-p`` för att ange antalet processer till 64.

Observera att ``msrsync`` endast kan skriva till och från lokala volymer. Källan och målet måste vara tillgängligt som lokala monteringar på den arbets station som används för att utfärda kommandot.

Följ dessa anvisningar för att använda ``msrsync`` för att fylla i Azure Blob Storage med Azure HPC-cache:

1. Installera ``msrsync`` och dess förutsättningar (``rsync`` och python 2,6 eller senare)
1. Fastställ det totala antalet filer och kataloger som ska kopieras.

   Använd till exempel verktyget ``prime.py`` med argument ```prime.py --directory /path/to/some/directory``` (tillgängligt genom att hämta <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Om du inte använder ``prime.py``kan du beräkna antalet objekt med verktyget GNU ``find`` enligt följande:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet objekt med 64 för att fastställa antalet objekt per process. Använd det här talet med alternativet ``-f`` för att ange buckets storlek när du kör kommandot.

1. Utfärda ``msrsync``-kommandot för att kopiera filer:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformat för att flytta 11 000-filer i 64-processer från/test/source-repository till/mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
