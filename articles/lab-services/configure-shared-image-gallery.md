---
title: Konfigurera ett delat avbildnings galleri i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar ett delat avbildnings galleri i Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6e0c1419e5656f184d27dce8d185a86bea71d173
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389955"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurera ett delat bildgalleri i Azure DevTest Labs
DevTest Labs stöder nu funktionen för [delad bild galleri](../virtual-machines/windows/shared-image-galleries.md) . Det gör det möjligt för labbanvändare att få åtkomst till avbildningar från en delad plats när de skapar labbresurser. Funktionen hjälper dig även att skapa ordning och struktur för dina anpassade hanterade VM-avbildningar. Funktionen för delad bild galleri stöder:

- Hanterad global replikering av avbildningar
- Versionshantering och gruppering av avbildningar för enklare hantering
- Säkerställ hög tillgänglighet för dina avbildningar med ZRS-konton (zonredundant lagring) i regioner med stöd för tillgänglighetszoner. ZRS ger bättre motståndskraft mot problem i enskilda zoner.
- Dela mellan prenumerationer, och även mellan klientorganisationer, med hjälp av rollbaserad åtkomstkontroll (RBAC).

Mer information finns i [dokumentationen för Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md). 
 
Om du har ett stort antal hanterade avbildningar som du vill göra tillgängliga i hela företaget kan du använda ett delat avbildningsgalleri som en lagringsplats. Det gör det enkelt att uppdatera och dela avbildningarna. Som labbägare kan du lägga till ett befintligt delat avbildningsgalleri till ditt labb. När galleriet är tillagt kan labbägare skapa datorer från de senaste avbildningarna. En viktig fördel med den här funktionen är att DevTest Labs nu kan dela avbildningar mellan olika labb, prenumerationer och regioner. 

> [!NOTE]
> Mer information om kostnader som är associerade med tjänsten för delad avbildning finns i [fakturering för delade avbildnings Galleri](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Överväganden
- Du kan bara koppla ett delat bild galleri till ett labb i taget. Om du vill koppla ett annat galleri måste du koppla från det befintliga ett och koppla ett annat. 
- DevTest Labs har för närvarande inte stöd för att ladda upp bilder till galleriet via labbet. 
- När du skapar en virtuell dator med hjälp av en delad avbildning av galleriet, använder DevTest Labs alltid den senaste publicerade versionen av den här avbildningen. Om en avbildning har flera versioner kan användaren dock välja att skapa en dator från en tidigare version genom att gå till fliken Avancerade inställningar när du skapar en virtuell dator.  
- Även om DevTest Labs automatiskt gör ett bästa försök att se till att delade avbildnings Gallerien replikerar avbildningar till den region där laboratoriet finns, är det inte alltid möjligt. För att undvika att användare har problem med att skapa virtuella datorer från de här avbildningarna, se till att avbildningarna redan har repliker ATS till Labbets region. "

## <a name="use-azure-portal"></a>Använda Azure-portalen
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster** i den vänstra navigerings menyn.
1. Välj **DevTest Labs** från listan.
1. I listan med labb väljer du ditt **labb**.
1. Välj **konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn.
1. Välj **delade avbildnings gallerier** under **Virtual Machine Bases** på den vänstra menyn.

    ![Menyn delade avbildnings gallerier](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Koppla ett befintligt delat avbildnings galleri till labbet genom att klicka på knappen **bifoga** och välja ditt galleri i list rutan.

    ![Bifoga](./media/configure-shared-image-gallery/attach-options.png)
1. Gå till det kopplade galleriet och konfigurera galleriet för att **Aktivera eller inaktivera** delade avbildningar för att skapa virtuella datorer. Välj ett bild galleri i listan om du vill konfigurera det. 

    Som standard **låter alla avbildningar användas som virtuella dator baser** anges till **Ja**. Det innebär att alla avbildningar som är tillgängliga i den bifogade delade avbildnings galleriet blir tillgängliga för en labb användare när du skapar en ny virtuell labb dator. Om åtkomst till vissa avbildningar behöver begränsas, ändra **Tillåt att alla avbildningar används som virtuella dator baser** till **Nej**, och välj de avbildningar som du vill tillåta när du skapar virtuella datorer och välj sedan knappen **Spara** .

    ![Aktivera eller inaktivera](./media/configure-shared-image-gallery/enable-disable.png)
1. Labb användare kan sedan skapa en virtuell dator med hjälp av de aktiverade avbildningarna genom att klicka på **+ Lägg till** och hitta avbildningen på sidan **Välj en bassida** .

    ![Labbanvändare](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager-mallar

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Bifoga ett delat avbildnings galleri i labbet
Om du använder en Azure Resource Manager mall för att koppla ett delat avbildnings galleri till labbet, måste du lägga till det under avsnittet resurser i Resource Manager-mallen, som du ser i följande exempel:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Ett exempel på en fullständig Resource Manager-mall finns i exemplen för Resource Manager-mallar i vår offentliga GitHub-lagringsplats: [Konfigurera ett delat avbildnings Galleri när du skapar ett labb](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Använd API

### <a name="shared-image-galleries---create-or-update"></a>Delade avbildnings gallerier – skapa eller uppdatera

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Delade avbildnings Galleri bilder – lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar en virtuell dator med hjälp av en avbildning från den bifogade delade avbildnings galleriet: [Skapa en virtuell dator med en delad avbildning från galleriet](add-vm-use-shared-image.md)
