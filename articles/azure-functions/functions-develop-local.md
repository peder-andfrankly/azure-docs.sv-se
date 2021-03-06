---
title: Utveckla och kör Azure Functions lokalt
description: Lär dig hur du kodar och testar Azure Functions på den lokala datorn innan du kör dem på Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230634"
---
# <a name="code-and-test-azure-functions-locally"></a>Kod och test Azure Functions lokalt

När du har möjlighet att utveckla och testa Azure Functions i [Azure Portal], föredrar många utvecklare en lokal utvecklings miljö. Funktionerna gör det enkelt att använda dina favorit kod redigerare och utvecklingsverktyg för att skapa och testa funktioner på den lokala datorn. Dina lokala funktioner kan ansluta till Live Azure-tjänster och du kan felsöka dem på din lokala dator med hjälp av körnings funktionen för fullständiga funktioner.

## <a name="local-development-environments"></a>Lokala utvecklings miljöer

Hur du utvecklar funktioner på din lokala dator beror på [språk](supported-languages.md) och verktygs inställningar. Miljöerna i följande tabell stöder lokal utveckling:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-reference-python.md) [](functions-create-first-function-powershell.md) [](functions-reference-node.md) [(klass bibliotek), skript (. CSX), C# ](functions-dotnet-class-library.md)Java Script, PowerShell, python [ C# ](functions-reference-csharp.md) | [Azure Functions-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till funktioner som stöder vs Code. Kräver kärn verktyg. Stöder utveckling av Linux, MacOS och Windows när du använder version 2. x av kärn verktygen. Mer information finns i [skapa din första funktion med Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Kommando tolk eller Terminal](functions-run-local.md) | [](functions-reference-python.md) [](functions-reference-powershell.md) [](functions-reference-node.md) [(klass bibliotek), skript (. CSX), C# ](functions-dotnet-class-library.md)Java Script, PowerShell, python [ C# ](functions-reference-csharp.md) | [Azure Functions Core tools] tillhandahåller kärn körning och mallar för att skapa funktioner som möjliggör lokal utveckling. Version 2. x stöder utveckling på Linux, MacOS och Windows. Alla miljöer förlitar sig på kärn verktyg för den lokala Functions-körningen. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(klass bibliotek)](functions-dotnet-class-library.md) | Azure Functions verktyg ingår i **Azure Development** -arbetsbelastningen för [Visual Studio 2019](https://www.visualstudio.com/vs/) och senare versioner. Gör att du kan kompilera funktioner i ett klass bibliotek och publicera. dll-filen på Azure. Innehåller kärn verktygen för lokal testning. Läs mer i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (olika) | [Java](functions-reference-java.md) | Integrerar med Core-verktyg för att möjliggöra utveckling av Java-funktioner. Version 2. x stöder utveckling på Linux, MacOS och Windows. Mer information finns i [skapa din första funktion med Java och maven](functions-create-first-java-maven.md). Har även stöd för utveckling med [Sol förmörkelse](functions-create-maven-eclipse.md) och [IntelliJ idé](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av de här lokala utvecklings miljöerna gör att du kan skapa Function app-projekt och använda fördefinierade funktions mallar för att skapa nya funktioner. Varje använder kärn verktygen så att du kan testa och felsöka dina funktioner mot Real tids körningen på din egen dator precis som med andra appar. Du kan också publicera ditt Function app-projekt från någon av dessa miljöer till Azure.  

## <a name="next-steps"></a>Nästa steg

+ Läs mer om lokal utveckling av kompilerade C# funktioner med visual Studio 2019 i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).
+ Mer information om lokal utveckling av funktioner med VS Code på en Mac-, Linux-eller Windows-dator finns i [distribuera Azure Functions från vs Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Mer information om hur du utvecklar funktioner från kommando tolken eller terminalen finns i [arbeta med Azure Functions Core tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
