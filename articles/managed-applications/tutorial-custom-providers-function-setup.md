---
title: Konfigurera Azure Functions för Azure-anpassade leverantörer
description: Den här självstudien går igenom hur du skapar en Azure Function-app och konfigurerar den så att den fungerar med Azure-anpassade leverantörer
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173024"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure Functions för Azure-anpassade leverantörer

En anpassad Provider är ett kontrakt mellan Azure och en slut punkt. Med anpassade providers kan du ändra arbets flöden i Azure. Den här självstudien visar hur du konfigurerar en Azure Function-app så att den fungerar som en anpassad Provider-slutpunkt.

## <a name="create-the-azure-function-app"></a>Skapa Azure Function-appen

> [!NOTE]
> I den här självstudien skapar du en enkel tjänst slut punkt som använder en Azure Function-app. En anpassad Provider kan dock använda en offentligt tillgänglig slut punkt. Alternativen omfattar Azure Logic Apps, Azure API Management och Web Apps funktionen i Azure App Service.

För att starta den här självstudien bör du först följa självstudien [skapa din första Azure Function-app i Azure Portal](../azure-functions/functions-create-first-azure-function.md). Den här självstudien skapar en .NET Core-webhook-funktion som kan ändras i Azure Portal. Det är även grunden för den aktuella självstudien.

## <a name="install-azure-table-storage-bindings"></a>Installera lagrings bindningar för Azure Table

Så här installerar du Azure Table Storage-bindningar:

1. Gå till fliken **integrera** för HttpTrigger.
1. Välj **+ ny Indatatyp**.
1. Välj **Azure-Table Storage**.
1. Installera Microsoft. Azure. WebJobs. Extensions. Storage-tillägget om det inte redan är installerat.
1. I rutan **tabell parameter namn** anger du **tableStorage**.
1. Skriv **myCustomResources**i rutan **tabell namn** .
1. Välj **Spara** för att spara den uppdaterade indataparametern.

![Översikt över anpassad Provider med tabell bindningar](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Uppdatera RESTful HTTP-metoder

Konfigurera Azure-funktionen så att den inkluderar den anpassade providern RESTful förfrågnings metoder:

1. Gå till fliken **integrera** för HttpTrigger.
1. Under **valda HTTP-metoder**väljer du **Hämta**, **post**, **ta bort**och **Placera**.

![Översikt över anpassad Provider visar HTTP-metoder](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Lägg till Azure Resource Manager NuGet-paket

> [!NOTE]
> Om C# projekt filen saknas i projekt katalogen kan du lägga till den manuellt. Eller så kommer det att visas efter att Microsoft. Azure. WebJobs. Extensions. Storage-tillägget har installerats i Function-appen.

Uppdatera sedan projekt filen C# så att den innehåller användbara NuGet-bibliotek. De här biblioteken gör det lättare att parsa inkommande begär Anden från anpassade providers. Följ stegen för att [lägga till tillägg från portalen](../azure-functions/install-update-binding-extensions-manual.md) och uppdatera C# projekt filen så att den innehåller följande paket referenser:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Följande XML-element är en exempel C# projekt fil:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure Function-app som fungerar som en Azure-anpassad Provider-slutpunkt.

Information om hur du skapar en RESTful anpassad Provider-slutpunkt finns [i Självstudier: Skapar en RESTful anpassad Provider-slutpunkt](./tutorial-custom-providers-function-authoring.md).

