---
title: Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten
description: Ansluta till ett Azure blockchain service Consortium-nätverk med Azure blockchain Development Kit for Ethereum-tillägget i Visual Studio Code
ms.date: 12/05/2019
ms.topic: quickstart
ms.reviewer: chrisseg
ms.openlocfilehash: 1bcdfdafa9253fc4dc20ccb7cc237cb794b0af53
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977101"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk

I den här snabb starten installerar och använder du tillägget Azure blockchain Development Kit för Ethereum Visual Studio Code (VS Code) för att ansluta till ett konsortium på Azure blockchain-tjänsten. Azure blockchain Development Kit fören klar hur du skapar, ansluter, bygger och distribuerar smarta kontrakt på Ethereum blockchain-redovisning.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* [Visual Studio-kod](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit för Ethereum-tillägg](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js-10.15. x eller högre](https://nodejs.org/download)
* [Git 2.10. x eller högre](https://git-scm.com)
* [Python-2.7.15](https://www.python.org/downloads/release/python-2715/) Lägg till python. exe i sökvägen. Python-2.7.15 i din sökväg krävs för Azure blockchain Development Kit.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI-6.0.0](https://github.com/trufflesuite/ganache-cli)

I Windows krävs en installerad C++ kompilator för Node-Gyp-modulen. Du kan använda MSBuild-verktygen:

* Om Visual Studio 2017 är installerat konfigurerar du NPM att använda MSBuild-verktygen med kommandot `npm config set msvs_version 2017 -g`
* Om Visual Studio 2019 är installerat ställer du in sökvägen för MS build-verktyg för NPM. Till exempel, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Annars installerar du fristående VS build-verktyg med `npm install --global windows-build-tools` i ett förhöjdt *Kör som administratörs* kommando gränssnitt.

Mer information om Node-Gyp finns i [nod-Gyp-lagringsplatsen på GitHub](https://github.com/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Kontrol lera Azure blockchain Development Kit-miljön

Azure blockchain Development Kit verifierar att utvecklings miljö kraven är uppfyllda. För att verifiera utvecklings miljön:

Från kommando paletten VS Code väljer du **Azure blockchain: Visa välkomst sida**.

Azure blockchain Development Kit kör ett verifierings skript som tar ungefär en minut att slutföra. Du kan visa utdata genom att välja **Terminal > ny terminal**. I meny raden i terminalen väljer du fliken **utdata** och **Azure-blockchain** i list rutan. Lyckad verifiering ser ut som på följande bild:

![Giltig utvecklings miljö](./media/connect-vscode/valid-environment.png)

 Om du saknar ett nödvändigt verktyg, en ny flik som heter **Azure blockchain Development Kit** , visar en lista över de verktyg som krävs för att ladda ned länkar.

![Appar som krävs av dev kit](./media/connect-vscode/required-apps.png)

Installera eventuella krav som saknas innan du fortsätter med snabb starten.

## <a name="connect-to-consortium-member"></a>Anslut till konsortiet medlem

Du kan ansluta till medlemmar i konsortiet med hjälp av tillägget Azure blockchain Development Kit VS Code. När du har anslutit till ett konsortium kan du kompilera, bygga och distribuera smarta kontrakt till en Azure blockchain service Consortium-medlem.

Om du inte har åtkomst till en Azure blockchain service Consortium-medlem måste du slutföra den nödvändiga [snabb starten: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md).

1. I fönstret VS Code-Utforskaren expanderar du tillägget **Azure blockchain** .
1. Välj **Anslut till nätverk**.

   ![Anslut till nätverk](./media/connect-vscode/connect-consortium.png)

    Om du uppmanas att ange Azure-autentisering följer du anvisningarna för att autentisera med hjälp av en webbläsare.
1. Välj **Azure blockchain-tjänsten** i list rutan kommando för palett.
1. Välj den prenumeration och resurs grupp som är kopplad till din Azure blockchain service Consortium-medlem.
1. Välj konsortiet i listan.

Konsortiet och blockchain-medlemmarna visas i det högra fältet i VS Code Explorer.

![Konsortiet som visas i Utforskaren](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Azure blockchain Development Kit för Ethereum VS Code Extension för att ansluta till ett konsortium på Azure blockchain-tjänsten. Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta avtal i Azure blockchain-tjänsten](send-transaction.md)