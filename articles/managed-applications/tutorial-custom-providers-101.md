---
title: Skapa anpassade åtgärder och resurser i Azure
description: Den här självstudien går igenom hur du skapar anpassade åtgärder och resurser i Azure Resource Manager. Det visar också hur anpassade arbets flöden samverkar med Azure Resource Manager mallar, Azure CLI, Azure Policy och Azure aktivitets logg.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172935"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Skapa anpassade åtgärder och resurser i Azure

En anpassad Provider är ett kontrakt mellan Azure och en slut punkt. Med anpassade providers kan du ändra arbets flöden i Azure genom att lägga till nya API: er i Azure Resource Manager. Med dessa anpassade API: er kan Resource Manager använda nya funktioner för distribution och hantering.

I den här självstudien går vi igenom ett enkelt exempel på hur du lägger till nya åtgärder och resurser i Azure och hur du integrerar dem.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure Functions för Azure-anpassade leverantörer

Del ett av den här självstudien beskriver hur du konfigurerar en Azure Function-app så att den fungerar med anpassade providers:

- [Konfigurera Azure Functions för Azure-anpassade leverantörer](./tutorial-custom-providers-function-setup.md)

Anpassade providers kan arbeta med alla offentliga URL: er.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Redigera en RESTful-slutpunkt för anpassade providers

Del två i den här självstudien beskriver hur du skapar en RESTful-slutpunkt för anpassade providers:

- [Redigera en RESTful-slutpunkt för anpassade providers](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Skapa och Använd en anpassad Provider

Del tre i den här självstudien beskriver hur du skapar en anpassad Provider och använder dess anpassade åtgärder och resurser:

- [Skapa och Använd en anpassad Provider](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om anpassade leverantörer och hur du skapar en. Om du vill fortsätta till nästa självstudie, [se Självstudier: Konfigurera Azure Functions för Azure-anpassade providers](./tutorial-custom-providers-function-setup.md).

Om du letar efter referenser eller en snabb start är här några användbara länkar:

- [Snabbstart: Skapa en Azure-anpassad resurs leverantör och distribuera anpassade resurser](./create-custom-provider.md)
- [Anvisningar: Lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Anvisningar: Lägga till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
