---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cd118def8b9caf97f26aaa5ad5dc1f6fb015316d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469226"
---
1. Från Azure Portal-menyn väljer du **skapa en resurs**.

    ![Lägg till resurs med hjälp av Azure Portal-menyn](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. På sidan **ny** väljer du **Compute** > **Funktionsapp**.

1. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Grundläggande inställningar](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsapp namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (Skift läges okänsligt), `0-9`och `-`.  |
    |**Publicera**| Kod | Alternativ för att publicera kod filer eller en Docker-behållare. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    |**Region**| Önskad region | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    Välj **Nästa: Hosting >** -knappen.

1. Ange följande inställningar för värd.

    ![Värd](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../articles/storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operativsystem**| Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **[Projektplan](../articles/azure-functions/functions-scale.md)** | Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. För den här typen av [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) värdtjänster betalar du bara för den tid som dina funktioner körs. När du använder en App Service-plan måste du hantera [funktionsappens skalning](../articles/azure-functions/functions-scale.md).  |

    Välj **Nästa: monitoring >** -knappen.

1. Ange följande inställningar för övervakning.

    ![Övervakning](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen kan du ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data. |

    Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. Välj **Skapa** för att etablera och distribuera funktionsappen.

1. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Distributionsmeddelande](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.
