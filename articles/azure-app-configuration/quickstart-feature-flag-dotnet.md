---
title: Snabb start för att lägga till funktions flaggor i .NET Framework appar | Microsoft Docs | Microsoft Docs
description: En snabb start för att lägga till funktions flaggor i .NET Framework appar och hantera dem i Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 0aecf2284e448f879bc20391c8528f8efde42d94
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184978"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Snabb start: Lägg till funktions flaggor i en .NET Framework app

I den här snabb starten inkluderar du Azure App konfiguration i en .NET Framework-app för att skapa en slut punkt till slut punkts implementering av funktions hantering. Du kan använda app Configuration service för att centralt lagra alla funktions flaggor och kontrol lera deras tillstånd. 

Biblioteken för .NET-funktions hantering utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek skapas ovanpå .NET-konfigurations systemet. De integreras sömlöst med app-konfigurationen via sin .NET-Konfigurationsprovider.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Skapa en .NET-konsolapp

1. Starta Visual Studio och välj **fil** > **nytt** > **projekt**.

1. I **skapa ett nytt projekt**filtrerar du på projekt typen **konsol** och klickar på **konsol program (.NET Framework)** . Klicka på **Nästa**.

1. Ange ett projekt namn i **Konfigurera ditt nya projekt**. Under **ramverk**väljer du **.NET Framework 4.7.1** eller högre. Klicka på **Skapa**.

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du och lägger till följande NuGet-paket i projektet. Om du inte hittar dem markerar du kryss rutan **Inkludera för hands version** .

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Öppna *program.cs* och Lägg till följande-uttryck:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera `Main`-metoden för att ansluta till app-konfigurationen, och ange alternativet `UseFeatureFlags` så att funktions flaggor hämtas. Visa sedan ett meddelande om funktions flaggan `Beta` är aktive rad.

    ```csharp
        static void Main(string[] args)
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options => 
                { 
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags(); 
                }).Build();
            
            IServiceCollection services = new ServiceCollection(); 
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement(); 
            IFeatureManager featureManager = services.BuildServiceProvider().GetRequiredService<IFeatureManager>(); 
            
            if (featureManager.IsEnabled("Beta")) 
            { 
                Console.WriteLine("Welcome to the beta"); 
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Ange en miljö variabel med namnet **ConnectionString** till anslutnings strängen för appens konfigurations lager. Om du använder kommando tolken i Windows kör du följande kommando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Om du använder Windows PowerShell kör du följande kommando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Starta om Visual Studio om du vill att ändringen ska börja gälla. 

1. Tryck på Ctrl + F5 för att skapa och köra-konsol programmet.

    ![App med funktions flagga aktive rad](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en funktions flagga i app-konfigurationen och använt den med en .NET Framework-konsol-app. Fortsätt till nästa självstudie om du vill veta mer om hur du uppdaterar funktions flaggor och andra konfigurations värden dynamiskt utan att starta om programmet.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-dotnet.md)