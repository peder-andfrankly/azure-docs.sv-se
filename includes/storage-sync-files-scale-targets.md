---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796201"
---
| Resurs | Målinrikta | Hård gräns |
|----------|--------------|------------|
| Tjänster för synkronisering av lagring per region | 20 lagrings tjänster för synkronisering | Ja |
| Synkronisera grupper per tjänst för synkronisering av lagring | 100 Sync-grupper | Ja |
| Registrerade servrar per tjänst för synkronisering av lagring | 99-servrar | Ja |
| Moln slut punkter per Sync-grupp | 1 moln slut punkt | Ja |
| Server slut punkter per Sync-grupp | 50 Server slut punkter | Nej |
| Server slut punkter per server | 30 Server slut punkter | Ja |
| Fil system objekt (kataloger och filer) per Sync-grupp | 100 000 000 objekt | Nej |
| Maximalt antal fil system objekt (kataloger och filer) i en katalog | 5 000 000 objekt | Ja |
| Säkerhets beskrivnings storlek för maximalt objekt (kataloger och filer) | 64 KiB | Ja |
| Filstorlek | 100 GiB | Nej |
| Minsta fil storlek för en fil i nivå | V9: baserat på fil systemets kluster storlek (kluster storlek för dubbla fil system). Om fil systemets kluster storlek till exempel är 4kb, kommer den minsta fil storleken att vara 8 KB.<br> V8 och äldre: 64 KiB  | Ja |

> [!Note]  
> En Azure File Sync-slutpunkt kan skala upp till storleken på en Azure-filresurs. Om storleks gränsen för Azure-filresursen uppnås går det inte att använda synkroniseringen.
