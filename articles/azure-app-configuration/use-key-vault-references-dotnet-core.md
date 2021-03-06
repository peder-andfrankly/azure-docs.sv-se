---
title: Självstudie för att använda Azure App konfiguration Key Vault referenser i en ASP.NET Core app | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure App konfigurations Key Vault referenser från en ASP.NET Core-app
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 992cface653bf3fe52afc7efa3f17573fcf91399
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469640"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Självstudie: använda Key Vault referenser i en ASP.NET Core app

I den här självstudien får du lära dig hur du använder Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Konfiguration av appar och Key Vault är kompletterande tjänster som används sida vid sida i de flesta program distributioner.

Med konfiguration av appar kan du använda tjänsterna tillsammans genom att skapa nycklar som refererar till värden som lagras i Key Vault. När app-konfigurationen skapar sådana nycklar lagrar den URI: er för Key Vault värden i stället för själva värdena.

Programmet använder klient leverantören för konfiguration av appar för att hämta Key Vault referenser, precis som för alla andra nycklar som lagras i app-konfigurationen. I det här fallet är de värden som lagras i app-konfigurationen URI: er som refererar till värdena i Key Vault. De är inte Key Vault värden eller autentiseringsuppgifter. Eftersom klient leverantören känner igen nycklarna som Key Vault referenser, använder den Key Vault för att hämta sina värden.

Ditt program ansvarar för att autentisera korrekt för både app-konfigurationen och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault referenser i din kod. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. Till exempel är [Visual Studio Code](https://code.visualstudio.com/) en plattforms oberoende kod redigerare som är tillgänglig för operativ systemen Windows, MacOS och Linux.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault.
> * Få åtkomst till värdet för den här nyckeln från ett ASP.NET Core-webbprogram.

## <a name="prerequisites"></a>Förutsättningar

Innan du startar den här självstudien installerar du [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **skapa en resurs** i det övre vänstra hörnet i Azure Portal:

    ![Utdata efter att nyckel valvet har skapats](./media/quickstarts/search-services.png)
1. I rutan Sök anger du **Key Vault**.
1. Välj **nyckel valv** till vänster i listan resultat.
1. I **nyckel valv**väljer du **Lägg till**.
1. Ange följande information till höger i **skapa nyckel valv**:
    - Välj en **prenumeration för att välja en** prenumeration.
    - I **resurs grupp**väljer du **Skapa ny** och anger ett resurs grupp namn.
    - I **Key Vault-namnet**krävs ett unikt namn. I den här självstudien anger du **contoso-vault2**.
    - Välj en plats i list rutan **region** .
1. Lämna de andra alternativen för att **skapa nyckel valv** med standardvärdena.
1. Välj **Skapa**.

I det här läget är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Utdata efter att nyckel valvet har skapats](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att testa Key Vault hämtning. Meddelandet kallas **meddelande**och du lagrar värdet "hej från Key Vault" i det.

1. Välj **hemligheter**på sidan Key Vault egenskaper.
1. Välj **generera/importera**.
1. Ange följande värden i fönstret **skapa en hemlighet** :
    - **Överförings alternativ**: ange **manuell**.
    - **Namn**: Ange ett **meddelande**.
    - **Värde**: ange **Hej från Key Vault**.
1. Lämna den andra **skapa en hemlig** egenskap med standardvärdena.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägg till en Key Vault referens till app-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser**och välj sedan den instans av app konfigurations arkiv som du skapade i snabb starten.

1. Välj **konfigurations Utforskaren**.

1. Välj **+ skapa** > **Key Vault-referens**och ange sedan följande värden:
    - **Nyckel**: Välj **TestApp: Settings: KeyVaultMessage**.
    - **Etikett**: lämna värdet tomt.
    - **Prenumeration**, **resurs grupp**och **nyckel valv**: Ange de värden som motsvarar dem i nyckel valvet som du skapade i föregående avsnitt.
    - **Hemlighet**: Välj det hemliga namnet **meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Key Vault

1. I den här självstudien använder du ett huvud namn för tjänsten för autentisering till Key Vault. Om du vill skapa tjänstens huvud namn använder du kommandot Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie med nyckel/värde-par:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando för att ge tjänstens huvud namn åtkomst till ditt nyckel valv:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Lägg till hemligheter för *clientId* och *ClientSecret* i hemligheter Manager, verktyget för att lagra känsliga data som du har lagt till i *. CSPROJ* -filen i [snabb start: skapa en ASP.net Core-app med Azure App konfiguration](./quickstart-aspnet-core-app.md). De här kommandona måste köras i samma katalog som *. CSPROJ* -filen.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Dessa Key Vault autentiseringsuppgifter används endast i ditt program. Programmet autentiseras direkt till Key Vault med dessa autentiseringsuppgifter. De skickas aldrig till appens konfigurations tjänst.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera din kod för att använda en Key Vault referens

1. Öppna *program.cs*och Lägg till referenser till följande nödvändiga paket:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Uppdatera metoden `CreateWebHostBuilder` för att använda app-konfiguration genom att anropa metoden `config.AddAzureAppConfiguration`. Inkludera `UseAzureKeyVault` alternativet för att skicka en ny `KeyVaultClient` referens till din Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. När du har initierat anslutningen till app-konfigurationen, skickade du `KeyVaultClient` referens till `UseAzureKeyVault`-metoden. Efter initieringen kan du komma åt värdena för Key Vault referenser på samma sätt som du kommer åt värdena för vanliga konfigurations nycklar för appar.

    Om du vill se hur den här processen fungerar öppnar du *index. cshtml* i **vyerna** > arbetsmappen. Ersätt innehållet med följande kod:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Du har åtkomst till värdet för Key Vault Reference **TestApp: Settings: KeyVaultMessage** på samma sätt som för konfiguration svärdet för **TestApp: Settings: Message**.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```
    dotnet build
    ```

1. När build-versionen har slutförts använder du följande kommando för att köra webbappen lokalt:

    ```
    dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till `http://localhost:5000`, vilket är standard-URL: en för webbappen som finns lokalt.

    ![Starta start av lokal app](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault. Fortsätt till nästa självstudie om du vill veta mer om hur du lägger till en Azure-hanterad tjänst identitet som effektiviserar åtkomsten till app-konfigurationen och Key Vault.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
