---
title: Distribuera associationer för Azure anpassad Provider med hjälp av princip
description: Lär dig mer om att distribuera associationer för en anpassad Provider med hjälp av Azure Policy-tjänsten.
author: msHich
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 94f7128a473266824242f52a9c50f09b2c24f1d4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932650"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Distribuera associationer för en anpassad Provider med hjälp av Azure policy

Azure-principer kan användas för att distribuera associationer för att koppla resurser till en anpassad Provider. I den här artikeln beskriver vi en inbyggd princip som distribuerar associationer och hur du kan använda den principen.

## <a name="built-in-policy-to-deploy-associations"></a>Inbyggd princip för att distribuera associationer

Att distribuera associationer för en anpassad Provider är en inbyggd princip som kan användas för att distribuera associationen för att koppla en resurs till en anpassad Provider. Principen accepterar tre parametrar:

- ID för anpassad Provider – detta ID är resurs-ID för den anpassade provider som resurserna måste kopplas till.
- Resurs typer som ska associeras – dessa resurs typer är listan över resurs typer som ska associeras med den anpassade providern. Du kan associera flera resurs typer till en anpassad Provider med samma princip.
- Prefix för associerings namn – den här strängen är det prefix som ska läggas till i namnet på den Associations resurs som skapas. Standardvärdet är "DeployedByPolicy".

Principen använder DeployIfNotExists-utvärdering. Den körs när en resurs leverantör har hanterat en begäran om att skapa eller uppdatera resurs och utvärderingen har returnerat en status kod som lyckats. Därefter distribueras Associations resursen med hjälp av en mall distribution.
Mer information om associationer finns i [Azure Custom providers-resurs onboarding](./concepts-custom-providers-resourceonboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Använda den inbyggda principen distribuera associationer 

### <a name="prerequisites"></a>Krav
Om den anpassade providern behöver behörighet till omfånget för principen för att utföra en åtgärd fungerar inte princip distributionen av Associations resursen utan att du beviljar behörigheterna.

### <a name="policy-assignment"></a>Principtilldelning
Om du vill använda den inbyggda principen skapar du en princip tilldelning och tilldelar distributions associationer för en anpassad Provider-princip. Principen identifierar då icke-kompatibla resurser och distribuerar associationen för dessa resurser.

![Tilldela inbyggd princip](media/builtin-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Få hjälp

Om du har frågor om utveckling av Azure-anpassade resurs leverantörer kan du prova att be dem om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har besvarats. kontrol lera först innan du publicerar. Lägg till taggen ```azure-custom-providers``` för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du använder en inbyggd princip för att distribuera associationer. Mer information finns i de här artiklarna:

- [Koncept: Azure Custom providers-resurs-onboarding](./concepts-custom-providers-resourceonboarding.md)
- [Självstudie: resurs onboarding med anpassade providers](./tutorial-custom-providers-resource-onboarding.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-custom-providers-101.md)
- [Snabb start: skapa en anpassad resurs leverantör och distribuera anpassade resurser](./create-custom-provider.md)
- [Gör så här: lägga till anpassade åtgärder i en Azure-REST API](./custom-providers-action-endpoint-how-to.md)
- [Gör så här: lägga till anpassade resurser i en Azure-REST API](./custom-providers-resources-endpoint-how-to.md)
