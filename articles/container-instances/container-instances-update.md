---
title: Uppdatera behållar grupp
description: Lär dig hur du uppdaterar pågående behållare i Azure Container Instances behållar grupper.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533304"
---
# <a name="update-containers-in-azure-container-instances"></a>Uppdatera behållare i Azure Container Instances

Under normal drift av dina behållar instanser kan du se till att du behöver uppdatera de behållare som körs i en [behållar grupp](container-instances-container-groups.md). Till exempel kanske du vill uppdatera avbildnings versionen, ändra ett DNS-namn, uppdatera miljövariabler eller uppdatera status för en behållare vars program har kraschat.

> [!NOTE]
> Avslutade eller borttagna behållar grupper kan inte uppdateras. När en behållar grupp har avslut ATS (har statusen lyckad eller misslyckad) eller har tagits bort måste gruppen distribueras som ny.

## <a name="update-a-container-group"></a>Uppdatera en behållar grupp

Uppdatera behållarna i en behållare grupp som körs genom att distribuera om en befintlig grupp med minst en ändrad egenskap. När du uppdaterar en behållar grupp startas alla behållare som körs i gruppen om på plats, vanligt vis på samma underliggande behållar värd.

Distribuera om en befintlig behållar grupp genom att utfärda kommandot CREATE (eller använda Azure Portal) och ange namnet på en befintlig grupp. Ändra minst en giltig egenskap för gruppen när du utfärdar kommandot CREATE för att utlösa omdistributionen och lämna de återstående egenskaperna oförändrade (eller Fortsätt att använda standardvärden). Alla behållar grupp egenskaper är inte giltiga för omdistribution. Se [egenskaper som kräver ta bort](#properties-that-require-container-delete) för en lista med egenskaper som inte stöds.

Följande Azure CLI-exempel uppdaterar en behållar grupp med en ny DNS-benämning. Eftersom egenskapen DNS-namn på gruppen är en som kan uppdateras, omdistribueras behållar gruppen och dess behållare startas om.

Första distribution med DNS-namn etikett för *program-mellanlagring*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Uppdatera behållar gruppen med en ny DNS-benämning, mina *program*och lämna de återstående egenskaperna oförändrade:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Uppdatera förmåner

Den främsta fördelen med att uppdatera en befintlig behållar grupp är snabbare distribution. När du distribuerar om en befintlig behållar grupp, hämtas dess behållar avbildnings lager från de som cachelagrats av den tidigare distributionen. I stället för att hämta alla bild lager från registret när det är gjort med nya distributioner, hämtas endast ändrade lager.

Program som baseras på större behållar avbildningar som Windows Server Core kan se avsevärd förbättring av distributions hastigheten när du uppdaterar i stället för att ta bort och distribuera nya.

## <a name="limitations"></a>Begränsningar

Alla egenskaper för en behållar grupp har inte stöd för uppdateringar. Om du vill ändra vissa egenskaper för en behållar grupp måste du först ta bort och sedan distribuera om gruppen. Mer information finns i [egenskaper som kräver borttagning av behållare](#properties-that-require-container-delete).

Alla behållare i en behållar grupp startas om när du uppdaterar behållar gruppen. Det går inte att utföra en uppdatering eller omstart på plats av en speciell behållare i en grupp med flera behållare.

IP-adressen för en behållare ändras normalt inte mellan uppdateringar, men det är inte säkert att den är oförändrad. Så länge behållar gruppen distribueras till samma underliggande värd, behåller behållar gruppen sin IP-adress. Även om det sällsynta, och medan Azure Container Instances gör att omdistribution till samma värd, finns det vissa Azure-interna händelser som kan leda till omdistribution till en annan värd. Använd alltid en DNS-etikett för behållar instanser för att undvika det här problemet.

Avslutade eller borttagna behållar grupper kan inte uppdateras. När en behållar grupp har stoppats (är i *avslutat* tillstånd) eller har tagits bort, distribueras gruppen som ny.

## <a name="properties-that-require-container-delete"></a>Egenskaper som kräver borttagning av behållare

Som tidigare nämnts kan inte alla behållar grupps egenskaper uppdateras. Om du till exempel vill ändra portarna eller starta om principen för en behållare måste du först ta bort behållar gruppen och sedan skapa den igen.

De här egenskaperna kräver att behållar gruppen tas bort innan omdistribution:

* OS-typ
* Processor
* Minne
* Starta om princip
* Portar

När du tar bort en behållar grupp och återskapar den, är den inte "omdistribuerad", men skapade ny. Alla bild lager hämtas från registret, inte från de som cachelagrats av en tidigare distribution. IP-adressen för behållaren kan också ändras på grund av att den distribueras till en annan underliggande värd.

## <a name="next-steps"></a>Nästa steg

Som nämns flera gånger i den här artikeln är **behållar gruppen**. Varje behållare i Azure Container Instances distribueras i en behållar grupp och behållar grupper kan innehålla fler än en behållare.

[Behållargrupper i Azure Container Instances](container-instances-container-groups.md)

[Distribuera en grupp med flera behållare](container-instances-multi-container-group.md)

[Stoppa eller starta behållare manuellt i Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
