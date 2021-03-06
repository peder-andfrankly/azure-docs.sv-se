---
title: Integrera REST API anspråk utbyten i en användar resa
titleSuffix: Azure AD B2C
description: Integrera REST API Claims-utbyten i Azure AD B2C användar resa som validering av användarindata.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3bea04ba077aebe9a52400a1292c5cd27c15b72e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950926"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrera REST API Claims-utbyten i Azure AD B2C användar resa som validering av användarindata

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med Identity Experience Framework, som ligger under Azure Active Directory B2C (Azure AD B2C), kan du integrera med ett RESTful-API i en användar resa. I den här genom gången lär du dig hur Azure AD B2C interagerar med .NET Framework RESTful Services (webb-API).

## <a name="introduction"></a>Introduktion

Genom att använda Azure AD B2C kan du lägga till din egen affärs logik till en användar resa genom att anropa din egen RESTful-tjänst. Med Identity Experience Framework skickas data till RESTful-tjänsten i en inloggad *anspråks samling och* tar emot data tillbaka från RESTful i en *utgående anspråks* samling. Med RESTful service integration kan du:

* **Verifiera indata från användaren**: den här åtgärden förhindrar att felaktiga data sparas i Azure AD. Om värdet från användaren inte är giltigt returnerar din RESTful-tjänst ett fel meddelande som uppmanar användaren att ange en post. Du kan till exempel kontrol lera att den e-postadress som användaren har angett finns i kundens databas.
* **Skriv över indata-anspråk**: om en användare till exempel anger det första namnet i gemener eller versaler, kan du formatera namnet med enbart den första bokstaven med versaler.
* **Utöka användar data genom att ytterligare integrera med branschspecifika program**: din RESTful-tjänst kan ta emot användarens e-postadress, fråga kundens databas och returnera användarens lojalitets nummer till Azure AD B2C. Retur anspråk kan lagras i användarens Azure AD-konto, utvärderas i nästa steg för att *dirigera*eller ingå i åtkomsttoken.
* **Kör anpassad affärs logik**: du kan skicka push-meddelanden, uppdatera företags databaser, köra en användarmigrering, hantera behörigheter, granska databaser och utföra andra åtgärder.

Du kan utforma integrationen med RESTful-tjänsterna på följande sätt:

* **Teknisk profil för validering**: anropet till RESTful-tjänsten sker i den tekniska verifierings profilen för den angivna tekniska profilen. Den tekniska verifierings profilen verifierar de data som anges av användaren innan användar resan flyttas framåt. Med den tekniska verifierings profilen kan du:
  * Skicka ingående anspråk.
  * Verifiera inaktuella anspråk och generera anpassade fel meddelanden.
  * Skicka anspråk för tillbaka utdata.

* **Anspråk Exchange**: den här designen liknar verifieringen av den tekniska profilen, men det sker i ett Orchestration-steg. Den här definitionen är begränsad till:
  * Skicka ingående anspråk.
  * Skicka anspråk för tillbaka utdata.

## <a name="restful-walkthrough"></a>RESTful-genom gång

I den här genom gången utvecklar du ett .NET Framework webb-API som validerar användarindata och ger ett användar förmåns nummer. Ditt program kan till exempel ge åtkomst till *Platinum-förmåner* baserat på förmåns numret.

Översikt:

* Utveckla RESTful-tjänsten (.NET Framework webb-API)
* Använda RESTful-tjänsten i användar resan
* Skicka inloggade anspråk och Läs dem i din kod
* Verifiera användarens förnamn
* Skicka tillbaka ett förmåns nummer
* Lägga till förmåns numret till en JSON Web Token (JWT)

## <a name="prerequisites"></a>Krav

Slutför stegen i artikeln [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) .

## <a name="step-1-create-an-aspnet-web-api"></a>Steg 1: skapa ett ASP.NET webb-API

1. Skapa ett projekt i Visual Studio genom att välja **fil** > **nytt** > **projekt**.
1. I fönstret **nytt projekt** väljer du **visuella C#**  > **webb** > **ASP.NET-webbprogram (.NET Framework)** .
1. I rutan **namn** anger du ett namn för programmet (till exempel *contoso. AADB2C. API*) och väljer sedan **OK**.

    ![Skapa ett nytt Visual Studio-projekt i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

1. I fönstret **nytt ASP.NET-webbprogram** väljer du en **webb-API** eller en **Azure API app** -mall.

    ![Välja en webb-API-mall i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Se till att autentiseringen är inställd på **Ingen autentisering**.
1. Klicka på **OK** för att skapa projektet.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Steg 2: Förbered REST API slut punkten

### <a name="step-21-add-data-models"></a>Steg 2,1: Lägg till data modeller

Modellerna representerar indata-anspråk och utgående anspråks data i din RESTful-tjänst. Din kod läser indata genom att deserialisera indata-anspråks modellen från en JSON- C# sträng till ett objekt (din modell). ASP.NET webb-API deserialiserar utgående anspråks modellen automatiskt till JSON och skriver sedan de serialiserade data till bröd texten i HTTP-svarsmeddelandet.

Skapa en modell som representerar indatamängds anspråk genom att göra följande:

1. Om Solution Explorer inte redan är öppet väljer du **visa** > **Solution Explorer**.
1. I Solution Explorer högerklickar du på mappen **Modeller**, välj **Lägg till** och sedan **Klass**.

    ![Lägg till klass meny alternativ som marker ATS i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

1. Namnge klassen `InputClaimsModel`och Lägg sedan till följande egenskaper i `InputClaimsModel`-klassen:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

1. Skapa en ny modell `OutputClaimsModel`och Lägg sedan till följande egenskaper i `OutputClaimsModel`-klassen:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Skapa en mer modell, `B2CResponseContent`, som du använder för att utlösa meddelanden om verifierings fel. Lägg till följande egenskaper i `B2CResponseContent`-klassen, ange de referenser som saknas och spara sedan filen:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Steg 2,2: Lägg till en kontrollant

I webb-API: et är en _kontrollant_ ett objekt som hanterar HTTP-begäranden. Styrenheten returnerar utgående anspråk eller, om det första namnet inte är giltigt, genererar ett HTTP-felmeddelande i konflikt.

1. I Solution Explorer högerklickar du på mappen **Styrenheter**. Välj sedan **Lägg till** och sedan **Styrenhet**.

    ![Lägga till en ny kontrollant i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. I fönstret **Lägg till Autogenerera** väljer du **webb-API-kontroll-tom**och väljer sedan **Lägg till**.

    ![Välja webb-API 2-kontrollant-tom i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. I fönstret **Lägg till kontrollant** namnger du kontrollantens **IdentityController**och väljer sedan **Lägg till**.

    ![Ange namnet på kontrollanten i Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Ramverk skapar en fil med namnet *IdentityController.cs* i mappen *controllers* .

1. Om *IdentityController.cs* -filen inte redan är öppen dubbelklickar du på den och ersätter sedan koden i filen med följande kod:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Steg 3: publicera projektet till Azure

1. I Solution Explorer högerklickar du på projektet **contoso. AADB2C. API** och väljer sedan **publicera**.

    ![Publicera till Microsoft Azure App Service med Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. I fönstret **publicera** väljer du **Microsoft Azure App Service**och väljer sedan **publicera**.

    ![Skapa nya Microsoft Azure App Service med Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Fönstret **skapa App Service** öppnas. I det här avsnittet skapar du alla nödvändiga Azure-resurser för att köra ASP.NET-webbappen i Azure.

    > [!TIP]
    > Mer information om hur du publicerar finns i [skapa en ASP.NET-webbapp i Azure](../app-service/app-service-web-get-started-dotnet-framework.md).

1. I rutan **namn på webbapp** skriver du ett unikt namn på appen (giltiga tecken är a-z, 0-9 och bindestreck (-). Webbappens webb adress är http://< app_name >. azurewebsites. NET, där *APP_NAME* är namnet på din webbapp. Du kan godkänna namnet som genereras automatiskt och som är unikt.

    ![Konfigurera App Service egenskaper](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Välj **skapa**för att börja skapa Azure-resurser.
    När ASP.NET-webbappen har skapats publicerar guiden den till Azure och startar sedan appen i standard webbläsaren.

1. Kopiera webbappens webb adress.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Steg 4: Lägg till det nya `loyaltyNumber`-anspråk till schemat för din TrustFrameworkExtensions. XML-fil

`loyaltyNumber`-anspråk har inte definierats i vårt schema ännu. Lägg till en definition i `<BuildingBlocks>`-elementet, som du hittar i början av filen *TrustFrameworkExtensions. XML* .

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Steg 5: Lägg till en anspråks leverantör

Varje anspråks leverantör måste ha en eller flera tekniska profiler som avgör vilka slut punkter och protokoll som behövs för att kommunicera med anspråks leverantören.

En anspråks leverantör kan ha flera tekniska profiler av olika anledningar. Till exempel kan flera tekniska profiler definieras eftersom anspråks leverantören har stöd för flera protokoll, men slut punkter kan ha varierande funktioner, eller också kan versioner innehålla anspråk som har en rad olika garanti nivåer. Det kan vara acceptabelt att släppa känsliga anspråk i en användar resa men inte i en annan.

Följande XML-kodfragment innehåller en nod för anspråks leverantör med två tekniska profiler:

* **TechnicalProfile-ID = "REST-API-signup"** : definierar din RESTful-tjänst.
  * `Proprietary` beskrivs som protokoll för en RESTful-baserad Provider.
  * `InputClaims` definierar de anspråk som ska skickas från Azure AD B2C till REST-tjänsten.

    I det här exemplet skickar innehållet i anspråks `givenName` till REST-tjänsten som `firstName`, innehållet i anspråks `surname` skickas till REST-tjänsten som `lastName`och `email` skickas som det är. `OutputClaims`-elementet definierar de anspråk som hämtas från RESTful-tjänsten tillbaka till Azure AD B2C.

* **TechnicalProfile-ID = "LocalAccountSignUpWithLogonEmail"** : lägger till en teknisk validerings profil till en befintlig teknisk profil (definieras i bas principen). Under registreringen av registreringen anropar verifierings den tekniska profilen föregående tekniska profil. Om RESTful-tjänsten returnerar ett HTTP-fel 409 (ett konflikt fel) visas fel meddelandet för användaren.

Leta upp noden `<ClaimsProviders>` och Lägg sedan till följande XML-kodfragment under noden `<ClaimsProviders>`:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Kommentarerna ovan `AuthenticationType` och `AllowInsecureAuthInProduction` anger vilka ändringar du ska göra när du flyttar till en produktions miljö. Information om hur du skyddar dina RESTful-API: er för produktion finns i [skydda RESTful-API: er med grundläggande autentisering](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) och [säkra RESTful-API: er med certifikatbaserad autentisering](active-directory-b2c-custom-rest-api-netfw-secure-cert.md).

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Steg 6: Lägg till `loyaltyNumber`-anspråk till den förlitande partens princip fil så att anspråk skickas till ditt program

Redigera filen *SignUpOrSignIn. XML* förlitande part (RP) och ändra TechnicalProfile-ID = "PolicyProfile"-elementet för att lägga till följande: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

När du har lagt till det nya anspråket ser koden för den förlitande parten ut så här:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Steg 7: överför principen till din klient organisation

1. I [Azure Portal](https://portal.azure.com)växlar du till [kontexten för din Azure AD B2C klient](active-directory-b2c-navigate-to-b2c-context.md)och öppnar sedan **Azure AD B2C**.

1. Välj **ramverk för identitets upplevelse**.

1. Öppna **alla principer**.

1. Välj **Ladda upp princip**.

1. Markera kryss rutan **Skriv över principen om den finns** .

1. Ladda upp filen TrustFrameworkExtensions. xml och kontrol lera att den klarar verifieringen.

1. Upprepa föregående steg med filen SignUpOrSignIn. xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Steg 8: testa den anpassade principen med hjälp av kör nu

1. Välj **Azure AD B2C inställningar**och gå sedan till **Identity Experience Framework**.

    > [!NOTE]
    > **Körning kräver nu** att minst ett program är förregistrerade på klienten. Information om hur du registrerar program finns i artikeln Azure AD B2C [komma igång](active-directory-b2c-get-started.md) eller i [program registrering](active-directory-b2c-app-registration.md) .

2. Öppna **B2C_1A_signup_signin**, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.

    ![Sidan B2C_1A_signup_signin anpassad princip i Azure Portal](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testa processen genom att skriva **test** i rutan **namn** .
    Azure AD B2C visar ett fel meddelande överst i fönstret.

    ![Testa det angivna namnet på inloggnings verifiering på registrerings Sidan](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. I rutan **namn** anger du ett namn (annat än "test").
    Azure AD B2C registrerar användaren och skickar sedan en loyaltyNumber till ditt program. Anteckna numret i detta JWT.

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>Valfritt Ladda ned fullständiga principfiler och kod

* När du har slutfört guiden [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)för din referens.

* Du kan ladda ned den fullständiga koden från [exempel Visual Studio-lösningen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Nästa steg

Din nästa uppgift är att skydda ditt RESTful-API med hjälp av Basic eller klientautentisering för klient certifikat. Information om hur du skyddar dina API: er finns i följande artiklar:

* [Skydda ditt RESTful-API med grundläggande autentisering (användar namn och lösen ord)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Skydda ditt RESTful-API med klient certifikat](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

Information om alla element som är tillgängliga i en RESTful teknisk profil finns i [referens: RESTful Technical Profile](restful-technical-profile.md).
