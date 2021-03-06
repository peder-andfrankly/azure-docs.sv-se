---
title: Metoder för användarmigrering
titleSuffix: Azure AD B2C
description: Diskuterar både grundläggande och avancerade koncept för användarmigrering med hjälp av Azure AD Graph API och om du vill med hjälp av Azure AD B2C anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b7d477b0704db3f23919281fd61328be114dae
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950229"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrera användare till Azure Active Directory B2C

När du migrerar identitets leverantören till Azure Active Directory B2C (Azure AD B2C) kan du också behöva migrera användar kontona. I den här artikeln förklaras hur du migrerar befintliga användar konton från vilken identitetsprovider som helst till Azure AD B2C. Artikeln är inte avsedd att vara förväntad, utan i stället beskrivs några få scenarier. Utvecklaren ansvarar för varje strategis lämplighet.

## <a name="user-migration-flows"></a>Flöden för användarmigrering

Med Azure AD B2C kan du migrera användare via [Azure AD-Graph API][B2C-GraphQuickStart]. Migreringen av användaren hamnar i två flöden:

- **Före migrering**: det här flödet gäller när du antingen har rensa åtkomst till en användares autentiseringsuppgifter (användar namn och lösen ord), eller om autentiseringsuppgifterna är krypterade, men du kan dekryptera dem. För migreringen sker att läsa användare från den gamla identitets leverantören och skapa nya konton i Azure AD B2C-katalogen.

- **Före migrering och lösen ords återställning**: det här flödet gäller när en användares lösen ord inte är tillgänglig. Exempel:
  - Lösen ordet lagras i HASH-format.
  - Lösen ordet lagras i en identitets leverantör som du inte kan komma åt. Den gamla identitets leverantören verifierar användarens autentiseringsuppgifter genom att anropa en webb tjänst.

I båda flödena kör du först processen före migreringen, läser användarna från din gamla identitetsprovider och skapar nya konton i Azure AD B2C-katalogen. Om du inte har lösen ordet skapar du kontot med ett lösen ord som skapas slumpmässigt. Sedan ber du användaren att ändra lösen ordet eller, när användaren loggar in för första gången, Azure AD B2C ber användaren återställa den.

## <a name="password-policy"></a>Lösen ords princip

Den Azure AD B2C lösen ords principen (för lokala konton) baseras på Azure AD-principen. Principerna Azure AD B2C för registrering eller inloggning och lösen ords återställning använder lösen ords styrkan "stark" och upphör aldrig att gälla lösen ord. Mer information finns i [lösen ords princip för Azure AD][AD-PasswordPolicies].

Om de konton som du vill migrera använder en svagare lösen ords styrka än den [starka lösen ords styrkan som tillämpas av Azure AD B2C][AD-PasswordPolicies], kan du inaktivera kravet på starkt lösen ord. Om du vill ändra standard lösen ords principen anger du `passwordPolicies` egenskapen till `DisableStrongPassword`. Du kan till exempel ändra begäran om att skapa användare på följande sätt:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Steg 1: Använd Azure AD-Graph API för att migrera användare

Du skapar Azure AD B2C användar kontot via Graph API (med lösen ordet eller med ett slumpmässigt lösen ord). I det här avsnittet beskrivs processen för att skapa användar konton i Azure AD B2C Directory med Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Steg 1,1: registrera ditt program i din klient organisation

För att kunna kommunicera med Graph API måste du först ha ett tjänst konto med administratörs behörighet. I Azure AD registrerar du ett program och aktiverar skriv åtkomst till katalogen. Programmets autentiseringsuppgifter är **program-ID** och **program hemlighet**. Programmet fungerar som själva, inte som en användare, för att anropa Graph API.

Registrera först ett program som du kan använda för hanterings uppgifter som migrering av användare.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Steg 1,2: ge administrativ behörighet till ditt program

Ge sedan programmet de Azure AD-Graph API behörigheter som krävs för att skriva till katalogen.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Steg 1,3: skapa program hemligheten

Skapa en klient hemlighet (nyckel) som ska användas av det program för användarmigrering som du konfigurerar i ett senare steg.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nu har du ett program med behörighet att skapa, läsa och uppdatera användare i din Azure AD B2C klient.

### <a name="step-14-optional-environment-cleanup"></a>Steg 1,4: (valfritt) miljö rensning

Behörigheten *läsa och skriva katalog data* inkluderar *inte* rätten att ta bort användare. För att ge ditt program möjlighet att ta bort användare (för att rensa din miljö) måste du utföra ett extra steg, som inbegriper att köra PowerShell för att ange administratörs behörighet för användar konton. Annars kan du gå vidare till nästa avsnitt.

> [!IMPORTANT]
> Du måste använda ett B2C klient administratörs konto som är *lokalt* för B2C-klienten. Konto namnets syntax är *admin\@contosob2c.onmicrosoft.com*.

I det här PowerShell-skriptet, som kräver [Azure AD PowerShell V2-modulen][AD-Powershell], gör du följande:

1. Anslut till din online tjänst. Det gör du genom att köra cmdleten `Connect-AzureAD` i kommando tolken för Windows PowerShell och ange dina autentiseringsuppgifter.

1. Använd **program-ID: t** för att tilldela programmet rollen som administratör för användar konton. De här rollerna har välkända identifierare, så allt du behöver göra är att ange ditt **program-ID** i skriptet.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Ändra `$AppId`-värdet med ditt Azure AD **-program-ID**.

## <a name="step-2-pre-migration-application-sample"></a>Steg 2: exempel på program för migrering

Du hittar kod exemplet för migrering i communityn `azure-ad-b2c/user-migration` GitHub-lagringsplatsen:

[Azure-AD-B2C/User-migration/före migrering][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Steg 2,1: redigera data filen för migrering

Exempel programmet använder en JSON-fil som innehåller dummy-användarnamn. När du har kört exemplet kan du ändra koden så att den använder data från din egen databas. Alternativt kan du exportera användar profilen till en JSON-fil och sedan ange att appen ska använda filen.

Om du vill redigera JSON-filen öppnar du `AADB2C.UserMigration.sln` Visual Studio-lösningen. Öppna `UsersData.json`-filen i `AADB2C.UserMigration`-projektet.

![Del av UsersData. JSON-filen som visar JSON-block av två användare](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Som du kan se innehåller filen en lista över användar enheter. Varje entitet för användare har följande egenskaper:

- e-post
- displayName
- firstName
- lastName
- lösen ord (kan vara tomt)

> [!NOTE]
> Vid kompileringen kopierar Visual Studio filen till `bin`-katalogen.

### <a name="step-22-configure-the-application-settings"></a>Steg 2,2: Konfigurera program inställningarna

Öppna filen *app. config* under `AADB2C.UserMigration` projektet. Ersätt följande app-inställningar med dina egna värden:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Användningen av en Azure Table-anslutningssträng beskrivs i nästa avsnitt.
> - Ditt B2C-klient namn är den domän som du angav när du skapade klienten, och det visas i Azure Portal. Klient namnet slutar vanligt vis med suffixet *. onmicrosoft.com* (till exempel *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Steg 2,3: kör för inmigrerings processen

Högerklicka på `AADB2C.UserMigration` lösning och återskapa sedan exemplet. Om du lyckas bör du nu ha en `UserMigration.exe` körbar fil som finns i `AADB2C.UserMigration\bin\Debug\net461`. Om du vill köra migreringsprocessen använder du någon av följande kommando rads parametrar:

- Om du vill **migrera användare med lösen ord**använder du kommandot `UserMigration.exe 1`.

- Om du vill **migrera användare med slumpmässigt lösen ord**använder du kommandot `UserMigration.exe 2`. Den här åtgärden skapar även en Azure Table-entitet. Senare konfigurerar du principen för att anropa tjänsten REST API. Tjänsten använder en Azure-tabell för att spåra och hantera migreringsprocessen.

![Kommando tolks fönstret visar utdata från kommandot UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Steg 2,4: kontrol lera processen före migreringen

Verifiera migreringen med någon av följande två metoder:

- Om du vill söka efter en användare efter visnings namn använder du Azure Portal:

   1. Öppna **Azure AD B2C**och välj sedan **användare**.
   1. I rutan Sök skriver du användarens visnings namn och visar sedan användarens profil.

- Om du vill hämta en användare via inloggnings-e-postadress använder du exempel programmet:

   1. Kör följande kommando:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Du kan också hämta en användare genom att visa namnet med hjälp av följande kommando: `UserMigration.exe 4 "<Display name>"`.

   1. Öppna filen UserProfile. json i en JSON-redigerare för att se användarens information.

      ![Filen UserProfile. JSON öppnas i Visual Studio Code Editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Steg 2,5: (valfritt) miljö rensning

Om du vill rensa din Azure AD-klient och ta bort användare från Azure AD-katalogen kör du kommandot `UserMigration.exe 5`.

> [!NOTE]
> * Om du vill rensa klienten konfigurerar du användar konto administratörs behörighet för ditt program.
> * I exemplet på migrering rensas alla användare som listas i JSON-filen.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Steg 2,6: Logga in med migrerade användare (med lösen ord)

När du har kört en för inmigrering med användar lösen ord, är kontona redo att användas och användarna kan logga in till ditt program med hjälp av Azure AD B2C. Om du inte har åtkomst till användar lösen ord kan du fortsätta till nästa avsnitt.

## <a name="step-3-help-users-reset-their-password"></a>Steg 3: hjälp användarna att återställa sina lösen ord

Om du migrerar användare med ett slumpmässigt lösen ord måste de återställa sina lösen ord. Du kan hjälpa dem att återställa lösen ordet genom att skicka ett välkomst meddelande med en länk för att återställa lösen ordet.

Följ dessa steg om du vill hämta en länk till din princip för lösen ords återställning. Den här proceduren förutsätter att du tidigare har skapat en [anpassad princip](active-directory-b2c-get-started-custom.md)för lösen ords återställning.

1. Välj den katalog som innehåller Azure AD B2C klienten med hjälp av filtret för **katalog + prenumeration** i det övre högra avsnittet av [Azure Portal](https://portal.azure.com).
1. Välj **Azure AD B2C** i den vänstra menyn (eller i **alla tjänster**).
1. Under **principer**väljer du **Identity Experience Framework**.
1. Välj din princip för lösen ords återställning. Till exempel *B2C_1A_PasswordReset*.
1. Välj ditt program i list rutan **Välj program** .

    > [!NOTE]
    > **Körning kräver nu** att minst ett program har registrerats i din klient organisation. Information om hur du registrerar program finns [i Självstudier: registrera ett program i Azure Active Directory B2C][B2C-AppRegister].

1. Kopiera den URL som visas i text rutan **Kör nu-slut punkt** och skicka den sedan till användarna.

    ![Sidan princip för lösen ords återställning med slut punkten för kör nu markerad](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Steg 4: (valfritt) ändra principen för att kontrol lera och ange status för användarmigrering

> [!NOTE]
> Om du vill kontrol lera och ändra status för användarmigrering måste du använda en anpassad princip. Konfigurations anvisningarna från [Kom igång med anpassade principer][B2C-GetStartedCustom] måste utföras.

När användarna försöker logga in utan att återställa lösen ordet först bör principen returnera ett eget fel meddelande. Exempel:

> *Ditt lösen ord har upphört att gälla. Om du vill återställa det väljer du länken Återställ lösen ord.*

Det här valfria steget kräver att Azure AD B2C anpassade principer används, enligt beskrivningen i artikeln [komma igång med anpassade principer][B2C-GetStartedCustom] .

I det här avsnittet ändrar du principen för att kontrol lera status för användarmigrering vid inloggning. Om användaren inte har ändrat lösen ordet returnerar du ett HTTP 409-fel meddelande som uppmanar användaren att välja länken **glömt lösen ordet?** .

För att spåra lösen ords ändringen använder du en Azure-tabell. När du kör för inmigreringen med kommando rads parametern `2`skapar du en entitet för användare i en Azure-tabell. Tjänsten gör följande:

- Vid inloggning anropar Azure AD B2C principen migrering av RESTful-tjänsten och skickar ett e-postmeddelande som ett inloggat anspråk. Tjänsten söker efter e-postadressen i Azure-tabellen. Om adressen finns, genererar tjänsten ett fel meddelande: *du måste ändra lösen ordet*.

- När användaren har ändrat lösen ordet tar du bort entiteten från Azure-tabellen.

> [!NOTE]
> Vi använder en Azure-tabell för att förenkla exemplet. Du kan lagra migrerings status i valfri databas eller som en anpassad egenskap i Azure AD B2C-kontot.

### <a name="41-update-your-application-setting"></a>4,1: uppdatera din program inställning

1. Om du vill testa RESTful API-demon öppnar du `AADB2C.UserMigration.sln` i Visual Studio.
1. Öppna filen *Web. config* i `AADB2C.UserMigration.API`-projektet. Ersätt inställningen med den som kon figurer ATS i [steg 2,2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Steg 4,2: Distribuera ditt webb program till Azure App Service

I Solution Explorer högerklickar du på `AADB2C.UserMigration.API`och väljer "publicera...". Följ anvisningarna för att publicera till Azure App Service. Mer information finns i [distribuera din app till Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Steg 4,3: Lägg till en teknisk profil och en teknisk profil validering i principen

1. Expandera "lösnings objekt" i Solution Explorer och öppna princip filen *TrustFrameworkExtensions. XML* .
1. Ändra `TenantId``PublicPolicyUri` och `<TenantId>` fälten från `yourtenant.onmicrosoft.com` till namnet på din klient organisation.
1. Under `<TechnicalProfile Id="login-NonInteractive">`-elementet ersätter du alla instanser av `ProxyIdentityExperienceFrameworkAppId` och `IdentityExperienceFrameworkAppId` med de program-ID: n som kon figurer ATS i [komma igång med anpassade principer][B2C-GetStartedCustom].
1. Leta upp följande XML-kodfragment under `<ClaimsProviders>`-noden. Ändra värdet för `ServiceUrl` att peka på Azure App Service URL.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Föregående tekniska profil definierar ett indatamängds-anspråk: `signInName` (skicka som-e-post). Vid inloggning skickas anspråket till din RESTful-slutpunkt.

När du har definierat den tekniska profilen för ditt RESTful-API konfigurerar du den befintliga `SelfAsserted-LocalAccountSignin-Email` tekniska profilen så att du kan anropa din REST API tekniska profil genom att åsidosätta den i din *TrustFrameworkExtensions. XML-* fil:

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Ändra sedan `Id` för `LocalAccountSignIn` teknisk profil till `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Steg 4,4: Ladda upp principen till din klient organisation

1. I [Azure Portal][Portal]växlar du till [kontexten för din Azure AD B2C klient][B2C-NavContext]och väljer sedan **Azure AD B2C**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **alla principer**.
1. Välj **Ladda upp princip**.
1. Markera kryss rutan **Skriv över principen om den finns** .
1. Ladda upp filen *TrustFrameworkExtensions. XML* och kontrol lera att den klarar verifieringen.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Steg 4,5: testa den anpassade principen med hjälp av kör nu

1. Välj **Azure AD B2C**och välj sedan **Identity Experience Framework**.
1. Öppna *B2C_1A_signup_signin*, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.
1. Ange autentiseringsuppgifterna för en av de migrerade användarna och välj sedan **Logga in**. Ditt REST API bör utlösa följande fel meddelande:

    ![Inloggnings registrerings sidan som visar fel meddelandet ändra lösen ord](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Steg 4,6: (valfritt) felsöka REST API

Du kan visa och övervaka loggnings information i nära real tid.

1. På RESTful-programmets inställningar-meny, under **övervakning**, väljer du **diagnostikloggar**.
1. Ange **program loggning (fil system)** till **på**.
1. Ange **nivån** **utförlig**.
1. Välj **Spara**

    ![Konfigurations sidan för diagnostikloggar i Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. På menyn **Inställningar** väljer du **logg ström**.
1. Kontrol lera utdata från RESTful-API: et.

> [!IMPORTANT]
> Använd bara diagnostikloggar när du utvecklar och testar. RESTful API-utdata kan innehålla konfidentiell information som inte bör exponeras i produktionen.

## <a name="optional-download-the-complete-policy-files"></a>Valfritt Hämta fullständiga principfiler

När du har slutfört [Kom igång med anpassade principer][B2C-GetStartedCustom] , rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler][UserMigrationSample-policy]för din referens.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
