---
title: Dela Galleri bilder över klienter i Azure
description: Lär dig hur du delar VM-avbildningar i Azure-klienter med hjälp av delade avbildnings gallerier.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034977"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela Galleri VM-avbildningar i Azure-klienter

Med delade avbildnings gallerier kan du dela avbildningar med RBAC. Du kan använda RBAC för att dela avbildningar i din klient organisation och till och med personer utanför din klient organisation. Mer information om det här enkla delnings alternativet finns i avsnittet [dela galleriet](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Du kan inte använda portalen för att distribuera en virtuell dator från en avbildning i en annan Azure-klient. Om du vill skapa en virtuell dator från en avbildning som delas mellan klienter, måste du använda Azure CLI eller [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Skapa en virtuell dator med Azure CLI

Logga in tjänstens huvud namn för klient organisation 1 med hjälp av appID, appens nyckel och ID för klient 1. Du kan använda `az account show --query "tenantId"` för att hämta klient-ID: n vid behov.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvud namn för klient 2 med hjälp av appID, appens nyckel och ID: t för klient organisationen 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa den virtuella datorn. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).