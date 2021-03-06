---
title: Skapa en Azure blockchain service-medlem – Azure CLI
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med hjälp av Azure CLI.
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 6a9673431c3da21b3ce645b9aff30506be1012f3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455959"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med Azure CLI

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

Skapa en blockchain-medlem i Azure blockchain-tjänsten som kör kvorumdisken i ett nytt konsortium. Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempel parametrarna med dina värden.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. Använd den resurs grupp som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där blockchain-medlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program.
| **ords** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.
| **Consortium** | Namnet på konsortiet att ansluta till eller skapa.
| **consortiumAccountPassword** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
| **skuName** | Nivå typ. Använd S0 för standard och B0 för Basic.

Det tar cirka 10 minuter att skapa blockchain-medlemmen och de resurser som stöds.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs grupp som du skapade för snabb starten.

Kör följande kommando för att ta bort resurs gruppen och alla relaterade resurser.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till ett konsortium på Azure blockchain-tjänsten.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)
