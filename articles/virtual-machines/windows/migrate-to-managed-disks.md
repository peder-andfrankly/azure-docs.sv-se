---
title: Migrera virtuella Azure-datorer till Managed Disks
description: Migrera virtuella Azure-datorer som skapats med ohanterade diskar i lagrings konton för att använda Managed Disks.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b907cf5bf64aa1a8a458a8d2b42c30dffd0dfd1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029801"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till Managed Disks i Azure

Azure Managed Disks fören klar lagrings hanteringen genom att ta bort behovet av separat hantering av lagrings konton.  Du kan också migrera dina befintliga virtuella Azure-datorer till Managed Disks för att dra nytta av bättre tillförlitlighet för virtuella datorer i en tillgänglighets uppsättning. Det garanterar att diskarna för olika virtuella datorer i en tillgänglighets uppsättning är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Den placerar automatiskt diskar med olika virtuella datorer i en tillgänglighets uppsättning i olika lagrings skalnings enheter (stämplar) som begränsar konsekvenserna av enskilda lagrings enhets enhets problem som orsakas av maskin-och program varu problem.
Utifrån dina behov kan du välja mellan fyra olika typer av lagrings alternativ. Information om tillgängliga disk typer finns i vår artikel [Välj en disktyp](disks-types.md)

## <a name="migration-scenarios"></a>Scenarier för migrering

Du kan migrera till Managed Disks i följande scenarier:

|Scenario  |Artikel  |
|---------|---------|
|Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till hanterade diskar     |[Konvertera virtuella datorer för att använda hanterade diskar](convert-unmanaged-to-managed-disks.md)         |
|Konvertera en enskild virtuell dator från klassisk till Resource Manager på Managed disks     |[Skapa en virtuell dator från en klassisk virtuell hård disk](create-vm-specialized-portal.md)         |
|Konvertera alla virtuella datorer i ett vNet från klassiskt läge till Resource Manager på Managed disks     |[Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och [konvertera sedan en virtuell dator från ohanterade diskar till Managed disks](convert-unmanaged-to-managed-disks.md)         |
|Uppgradera virtuella datorer med ohanterade standard diskar till virtuella datorer med hanterade Premium-diskar     | Konvertera först [en virtuell Windows-dator från ohanterade diskar till hanterade diskar](convert-unmanaged-to-managed-disks.md). [Uppdatera sedan lagrings typen för en hanterad disk](convert-disk-storage.md).         |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Managed disks](managed-disks-overview.md)
- Läs [om prissättningen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
