---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/12/2019
ms.custom: include file
ms.openlocfilehash: 4ed5be09d952d4d64c269e3eaf698ad7a74fffdd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014037"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj fältet start sida och sedan **+ skapa en resurs**. 

   [![Expandera fältet start sida och välj sedan + skapa en resurs](./media/create-digital-twins-portal/create-a-resource.png)](./media/create-digital-twins-portal/create-a-resource.png#lightbox)

1. Sök efter **digitala dubbla**och välj **digitala dubbla**. 

   [![val för att skapa en ny Digitals dubbla instanser](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

   Du kan också välja **Sakernas Internet**och välja **digitala dubbla (för hands version)** .

1. Välj **Skapa** för att starta distributionsprocessen.

   [![skapa och bekräfta distributionen av resursen](./media/create-digital-twins-portal/create-and-confirm-resource.png)](./media/create-digital-twins-portal/create-and-confirm-resource.png#lightbox)

1. I fönstret **Digital Twins** anger du följande information:
   * **Resursnamn**: Skapa ett unikt namn på Digital Twins-instansen.
   * **Prenumeration**: Välj den prenumeration som du vill använda för att skapa Digital Twins-instansen. 
   * **Resursgrupp**: Välj eller skapa en [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) för Digital Twins-instansen.
   * **Plats**: Välj den plats som är närmast enheten.

     [![Digitals sammanflätade fönster med angiven information](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Granska din Digital Twins-information och välj sedan **Skapa**. Det kan ta några minuter för Digital Twins-instansen att skapas. Du kan övervaka förloppet i **meddelandefönstret**.

1. Öppna **översiktsfönstret** för Digital Twins-instansen. Observera länken under **Hanterings-API**. URL: en för **hanterings-API** : n formateras som: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Denna URL tar dig till dokumentationen för Azure Digital Twins REST API som gäller för din instans. Läs om [hur du använder Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md) för att få reda på hur du läser och använder den här API-dokumentationen. Kopiera och ändra **hanterings-API** -URL: en till det här formatet: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Programmet använder den ändra URL:en som bas-URL för att få åtkomst till instansen. Kopiera denna ändrade URL till en temporär fil. Du behöver URL:en i nästa avsnitt.

   [Översikt över ![Management API](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)