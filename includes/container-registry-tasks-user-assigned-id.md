---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642116"
---
### <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med kommandot [AZ Identity Create][az-identity-create] . Du kan använda samma resurs grupp som du använde tidigare för att skapa ett behållar register, eller en annan.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Om du vill konfigurera den användarspecifika identiteten i följande steg, använder du kommandot [AZ Identity show][az-identity-show] för att lagra identitetens resurs-ID, tjänstens huvud NAMNS-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show