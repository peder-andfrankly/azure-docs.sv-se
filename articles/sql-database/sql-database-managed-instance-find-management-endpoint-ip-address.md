---
title: Identifiera slut punkt för hantering av hanterade instanser
description: Lär dig hur du hämtar en offentlig IP-adress för Azure SQL Database Hanterad instans hantering och kontrollerar dess inbyggda brand Väggs skydd
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825721"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Fastställa IP-adressen för hanterings slut punkten

Den Azure SQL Database hanterade instansens virtuella kluster innehåller en hanterings slut punkt som Microsoft använder för hanterings åtgärder. Hanterings slut punkten skyddas med en inbyggd brand vägg på nätverks nivå och ömsesidig certifikat verifiering på program nivå. Du kan fastställa hanterings slut punktens IP-adress, men du kan inte komma åt den här slut punkten.

För att fastställa IP-adressen för hantering gör du en DNS-sökning på den hanterade instansen med fullständigt domän namn: `mi-name.zone_id.database.windows.net`. Detta kommer att returnera en DNS-post som liknar `trx.region-a.worker.vnet.database.windows.net`. Du kan sedan göra en DNS-sökning på detta FQDN med ". VNet" borttaget. Detta kommer att returnera hanterings-IP-adressen. 

Den här PowerShell gör allt åt dig om du ersätter \<MI FQDN-\> med DNS-posten för din hanterade instans: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om hanterade instanser och anslutningar finns [Azure SQL Database arkitektur för hanterad instans anslutning](sql-database-managed-instance-connectivity-architecture.md).
