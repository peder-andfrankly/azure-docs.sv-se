---
title: Skriptexempel för Azure CLI – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI
description: Skriptexempel för Azure CLI – Kopiera (flytta) en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 5c83ce8d65be01156d50061d7f0a8287ddbfa1eb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039989"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopiera en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicks bild i Premium Storage (Premium_LRS) till standard lagring (Standard_LRS eller Standard_ZRS) för att minska kostnaderna.
1. Migrera en ögonblicks bild från lokalt redundant lagring (Premium_LRS Standard_LRS) till zonens redundanta lagring (Standard_ZRS) för att dra nytta av ZRS-lagringens högre tillförlitlighet.
1. Flytta en ögonblicks bild till en annan prenumeration i samma region för längre kvarhållning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av ögonblicksbildens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera ögonblicksbilden till en annan prenumeration.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Kopierar en ögonblicksbild genom att skapa en ögonblicksbild i en annan prenumeration med ID och namn på den överordnade ögonblicksbilden.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
