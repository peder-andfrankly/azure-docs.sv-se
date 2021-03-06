---
author: cynthn
ms.author: cynthn
ms.date: 11/25/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2a763bbd50f009ae469be889e6ebae0b0d90848b
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795951"
---
Med standardiserade avbildningar av virtuella datorer kan organisationer migrera till molnet och säkerställa konsekvens i distributionerna. Bilder innehåller vanligt vis fördefinierade säkerhets-och konfigurations inställningar och nödvändig program vara. Att konfigurera din egen avbildnings pipeline kräver tid, infrastruktur och konfiguration, men med Azure VM Image Builder får du bara en enkel konfiguration som beskriver avbildningen, skickar den till tjänsten och avbildningen skapas och distribueras.
 
Med Azures avbildnings verktyg för virtuella datorer (Azure Image Builder) kan du starta med en Windows-eller Linux-baserad Azure Marketplace-avbildning, befintliga anpassade avbildningar eller Red Hat Enterprise Linux (RHEL) ISO och börja lägga till dina egna anpassningar. Eftersom Image Builder bygger på [HashiCorp Packer](https://packer.io/), kan du också importera befintliga paket i Shell-Provisioning-skripten. Du kan också ange var du vill att dina avbildningar ska finnas i [Azures Galleri för delad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), som en hanterad avbildning eller en virtuell hård disk.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Förhandsversionsfunktioner

I för hands versionen stöds dessa funktioner:

- Skapandet av de gyllene bas linje avbildningarna, som innehåller din minsta säkerhets-och företags konfiguration, och gör det möjligt för avdelningar att anpassa dem ytterligare efter deras behov.
- Korrigering av befintliga avbildningar gör att du kontinuerligt kan korrigera befintliga anpassade avbildningar i Image Builder.
- Integrering med Azures delade avbildnings Galleri, gör att du kan distribuera, version och skala avbildningar globalt och ger dig ett avbildnings hanterings system.
- Integrering med befintliga avbildningar skapar pipeliner, anropar bara Image Builder från din pipeline eller Använd den enkla för hands versionen av DevOps-aktiviteten i Image Builder.
- Migrera en befintlig avbildnings anpassnings pipeline till Azure. Använd dina befintliga skript, kommandon och processer för att anpassa avbildningar.
- Använd Red Hat för att få ditt prenumerations stöd. Skapa Red Hat Enterprise-avbildningar för användning med dina kvalificerade, oanvända Red Hat-prenumerationer.
- Skapa avbildningar i VHD-format.
 

## <a name="regions"></a>Regioner
Azure Image Builder-tjänsten är tillgänglig för för hands version i dessa regioner. Avbildningar kan distribueras utanför dessa regioner.
- USA, östra
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2

## <a name="os-support"></a>OS-stöd
AIB kommer att ha stöd för Azure Marketplace Base OS-avbildningar:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6
- CentOS 7,6
- Windows 10 RS5 Enterprise/Professional/Enterprise för Virtual Desktop (EVD) 
- Windows 2016
- Windows 2019

AIB kommer att stödja RHEL ISO som källa för:
- RHEL 7,3
- RHEL 7,4
- RHEL 7.5

RHEL 7,6-ISO stöds inte, men testas.

## <a name="how-it-works"></a>Så här fungerar det


![Konceptuell ritning i Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure Image Builder är en fullständigt hanterad Azure-tjänst som kan nås av en Azure-adressresurs. Azure Image Builder-processen har tre huvud delar: källa, anpassa och distribuera, dessa representeras i en mall. Diagrammet nedan visar komponenterna, med några av deras egenskaper. 
 


**Image Builder-process** 

![Konceptuell ritning i Azure Image Builder-processen](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Skapa avbildnings mal len som en. JSON-fil. Den här. JSON-filen innehåller information om avbildningens källa, anpassningar och distribution. Det finns flera exempel i [Azure Image Builder GitHub-lagringsplatsen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Skicka den till tjänsten. då skapas en avbildnings mal len artefakt i den resurs grupp som du anger. I bakgrunden kommer Image Builder att ladda ned käll avbildningen eller ISO och skript efter behov. De lagras i en separat resurs grupp som skapas automatiskt i din prenumeration i formatet: IT_\<DestinationResourceGroup > _\<TemplateName >. 
1. När du har skapat avbildnings mal len kan du skapa avbildningen. I Background Image Builder används mall-och källfilerna för att skapa en virtuell dator (standard storlek: Standard_D1_v2), nätverk, offentlig IP, NSG och lagring i IT_\<DestinationResourceGroup > _\<TemplateName > resurs grupp.
1. Som en del av avbildningen distribuerar Image Builder avbildningen enligt mallen och tar sedan bort de ytterligare resurserna i IT_\<DestinationResourceGroup > _\<TemplateName > resurs grupp som har skapats för processen.


## <a name="permissions"></a>Behörigheter

Om du vill tillåta att Azure VM Image Builder distribuerar avbildningar till antingen de hanterade avbildningarna eller till ett delat avbildnings Galleri måste du ange deltagar behörighet för tjänsten Azure Virtual Machine Image Builder (app-ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) i resurs grupperna. 

Om du använder en befintlig anpassad hanterad avbildnings-eller avbildnings version behöver Azure Image Builder minst ' Reader '-åtkomst till dessa resurs grupper.

Du kan tilldela åtkomst med hjälp av Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Om tjänst kontot inte hittas kan det betyda att prenumerationen där du lägger till roll tilldelningen ännu inte har registrerats för resurs leverantören.


## <a name="costs"></a>Kostnader
Du kommer att ådra dig några beräknings-, nätverks-och lagrings kostnader när du skapar, skapar och lagrar avbildningar med Azure Image Builder. Dessa kostnader liknar kostnaderna för att skapa anpassade avbildningar manuellt. För resurserna debiteras du enligt dina Azure-priser. 

Under processen för att skapa avbildningar laddas filerna ned och lagras i `IT_<DestinationResourceGroup>_<TemplateName>` resurs grupp, vilket innebär en liten lagrings kostnad. Om du inte vill behålla dessa raderar du **avbildnings mal len** efter att avbildningen har byggts.
 
Image Builder skapar en virtuell dator med en D1v2 VM-storlek, och lagrings utrymme och nätverk som krävs för den virtuella datorn. Dessa resurser kommer att vara sist under Bygg processen och tas bort när avbildnings verktyget har skapat avbildningen. 
 
Azure Image Builder distribuerar avbildningen till dina valda regioner, vilket kan innebära att det tar betalt för nätverket.
 
## <a name="next-steps"></a>Nästa steg 
 
Om du vill prova Azure Image Builder kan du läsa artikeln om att skapa [Linux](../articles/virtual-machines/linux/image-builder.md) -eller [Windows](../articles/virtual-machines/windows/image-builder.md) -avbildningar.
 
 
