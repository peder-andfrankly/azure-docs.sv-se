---
title: Hantera IoT Central från Azure CLI | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar ditt IoT Central program med CLI. Du kan visa, ändra och ta bort programmet med CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c5622f32dbf849b9a21a1fd2e458f35b8aa1d098
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480387"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure CLI](/cli/azure/) för att hantera dina program.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure CLI på den lokala datorn kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). När du kör Azure CLI lokalt använder du kommandot **AZ login** för att logga in på Azure innan du provar kommandona i den här artikeln.

## <a name="create-an-application"></a>Skapa ett program

Använd kommandot [AZ iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) för att skapa ett IoT Central-program i din Azure-prenumeration. Exempel:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

De här kommandona skapar först en resurs grupp på platsen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med kommandot **AZ iotcentral app Create** :

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Resurs gruppen som innehåller programmet. Den här resurs gruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder detta kommando platsen från resurs gruppen. För närvarande kan du skapa ett IoT Central-program i **USA**, **Australien**, **Asien och Stillahavsområdet**eller på platser i **Europa** . |
| name              | Namnet på programmet i Azure Portal. |
| under domän         | Under domänen i programmets URL. I exemplet är programmets URL https://mysubdomain.azureiotcentral.com. |
| sku               | För närvarande är det enda värdet **S1** (standard nivån). Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| mall          | Den program mal len som ska användas. Mer information finns i följande tabell: |
| visnings namn      | Namnet på programmet som det visas i användar gränssnittet. |

**Programmallar med allmänt tillgängliga funktioner**

| Mallnamn            | Beskrivning |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |
| iotc-demo@1.0.0          | Skapar ett program som innehåller en mall för enheter som redan har skapats för en kylande varuautomat. Använd den här mallen för att börja utforska Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du är en enhets utvecklare som experimenterar med någon av dessa enheter. |


**Programmallar med funktioner för offentlig för hands version**

| Mallnamn            | Beskrivning |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Skapar ett tomt för hands versions program för plug-and-Play som du kan fylla i med dina egna enhets mallar och enheter. |
| iotc-condition@1.0.0     | Skapar ett program med en mall för övervakning av villkor i butiken. Använd den här mallen för att ansluta och övervaka lagrings miljön. |
| iotc-consumption@1.0.0   | Skapar ett program med en övervaknings mall för vattenkonsumtion. Använd den här mallen för att övervaka och kontrol lera vatten flödet. |
| iotc-distribution@1.0.0  | Skapar ett program med en mall för digital distribution. Använd den här mallen för att förbättra lagrets uteffekts effektivitet genom att digitala nyckel till gångar och åtgärder. |
| iotc-inventory@1.0.0     | Skapar ett program med en mall för smart inventerings hantering. Använd den här mallen för att automatisera mottagning, produkt förflyttning, cyklisk inventering och spårning av sensorer. |
| iotc-logistics@1.0.0     | Skapar ett program med en ansluten logistik mall. Använd den här mallen för att spåra din försändelse i real tid över luft, vatten och mark med plats-och villkors övervakning. |
| iotc-meter@1.0.0         | Skapar ett program med en mall för övervakning av smarta mätare. Använd den här mallen för att övervaka energi förbrukning, nätverks status och identifiera trender för att förbättra kund support och hantering av Smart avläsning.  |
| iotc-patient@1.0.0       | Skapar ett program med en mall för kontinuerliga patient övervakning. Använd den här mallen för att utöka patient vård, åter användning och hantering av sjukdomar. |
| iotc-power@1.0.0         | Skapar ett program med en övervaknings mall för sol panelen. Använd den här mallen för att övervaka status för sol panelen, utveckling av energi produktion. |
| iotc-quality@1.0.0       | Skapar ett program med en mall för övervakning av vatten kvalitet. Använd den här mallen om du vill övervaka vatten kvaliteten digitalt.|
| iotc-store@1.0.0         | Skapar ett program med en mall för analys i butiken – check. Använd den här mallen för att övervaka och hantera kassa flödet inuti ditt lager. |
| iotc-waste@1.0.0         | Skapar ett program med en mall för anslutet avfalls hantering. Använd den här mallen för att övervaka avfalls platser och operatörer för sändnings fält. |

> [!NOTE]
> För hands versions programmen är för närvarande endast tillgängliga i **Europa** och **USA** platser.

## <a name="view-your-applications"></a>Visa dina program

Använd kommandot [AZ iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) för att visa en lista över IoT Central program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd kommandot [AZ iotcentral app Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) för att uppdatera metadata för ett IoT Central-program. Om du till exempel vill ändra visnings namnet för ditt program:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd kommandot [AZ iotcentral app Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) för att ta bort ett IoT Central-program. Exempel:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure CLI rekommenderar vi följande steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
