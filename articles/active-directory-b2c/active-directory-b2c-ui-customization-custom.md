---
title: Anpassa användar gränssnittet för din app med en anpassad princip
titleSuffix: Azure AD B2C
description: Läs mer om hur du anpassar ett användar gränssnitt med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1ac0f59ea709e25f3d71a78ece5ebf40690bd3be
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949634"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du har slutfört den här artikeln har du en anpassad princip för registrering och inloggning med ditt varumärke och utseende. Med Azure Active Directory B2C (Azure AD B2C) får du nästan fullständig kontroll över HTML-och CSS-innehållet som presenteras för användarna. När du använder en anpassad princip kan du konfigurera UI-anpassning i XML i stället för att använda kontroller i Azure Portal.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

## <a name="page-ui-customization"></a>Anpassning av sid gränssnitt

Med hjälp av anpassnings funktionen för sid gränssnitt kan du anpassa utseendet på en anpassad princip. Du kan även hålla varumärke och grafik konsekventa mellan programmet och Azure AD B2C.

Så här fungerar det: Azure AD B2C kör kod i kundens webbläsare och använder ett modernt sätt som kallas [CORS (Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/). Först anger du en URL i den anpassade principen med anpassat HTML-innehåll. Azure AD B2C sammanfogar UI-element med HTML-innehåll som läses in från din URL och visar sedan sidan för kunden.

## <a name="create-your-html5-content"></a>Skapa ditt HTML5-innehåll

Skapa HTML-innehåll med produktens varumärke namn i rubriken.

1. Kopiera följande HTML-kodfragment. Den är välformulerad HTML5 med ett tomt element som kallas *\<div ID = "API"\>\</div\>* som finns i\<*Body\>* taggar. Det här elementet anger var Azure AD B2C innehåll ska infogas.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Klistra in det kopierade kodfragmentet i en text redigerare och spara sedan filen som *Customize-UI. html*.

> [!NOTE]
> HTML-formulärets element tas bort på grund av säkerhets begränsningar om du använder login.microsoftonline.com. Använd b2clogin.com om du vill använda HTML-formulär element i ditt anpassade HTML-innehåll. Se [använda b2clogin.com](b2clogin.md) för andra förmåner.

## <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob storage-konto

>[!NOTE]
> I den här artikeln använder vi Azure Blob Storage för att vara värd för vårt innehåll. Du kan välja att vara värd för ditt innehåll på en webb server, men du måste [Aktivera CORS på din webb server](https://enable-cors.org/server.html).

Utför följande steg för att vara värd för detta HTML-innehåll i Blob Storage:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. På menyn **hubb** väljer du **ny** > **lagring** > **lagrings konto**.
1. Välj en **prenumeration** för ditt lagrings konto.
1. Skapa en **resurs grupp** eller Välj en befintlig.
1. Ange ett unikt **namn** för ditt lagrings konto.
1. Välj den **geografiska platsen** för ditt lagrings konto.
1. **Distributions modellen** kan vara **Resource Manager**.
1. **Prestanda** kan vara **standard**.
1. Ändra **konto typ** till **Blob Storage**.
1. **Replikeringen** kan förbli **RA-GRS**.
1. **Åtkomst nivån** kan vara **aktiv**.
1. Klicka på **Granska + skapa** för att skapa lagrings kontot.
    När distributionen är klar öppnas sidan **lagrings konto** automatiskt.

## <a name="create-a-container"></a>Skapa en container

Utför följande steg för att skapa en offentlig behållare i Blob Storage:

1. Under **BLOB service** på menyn till vänster väljer du **blobbar**.
1. Klicka på **+ container**.
1. I **namn**anger du *root*. Detta kan vara ett namn som du väljer, till exempel *wingtiptoys*, men vi använder *roten* i det här exemplet för enkelhetens skull.
1. För **offentlig åtkomst nivå**väljer du **BLOB**och sedan **OK**.
1. Klicka på **rot** för att öppna den nya behållaren.
1. Klicka på **Överför**.
1. Klicka på mappikonen bredvid **Välj en fil**.
1. Navigera till och välj **Customize-UI. html** som du skapade tidigare i avsnittet sid UI anpassning.
1. Om du vill överföra till en undermapp expanderar du **Avancerat** och anger ett mappnamn i **mappen överför till**.
1. Välj **Överför**.
1. Välj den **Customize-UI. html-** blob som du laddade upp.
1. Till höger om text rutan **URL** väljer du ikonen **Kopiera till Urklipp** för att kopiera webb adressen till Urklipp.
1. I webbläsaren navigerar du till den URL som du kopierade för att verifiera att blobben du överförde är tillgänglig. Om det inte går att komma åt, till exempel om du stöter på ett `ResourceNotFound` fel, se till att behållarens åtkomst typ är inställd på **BLOB**.

## <a name="configure-cors"></a>Konfigurera CORS

Konfigurera Blob Storage för resurs delning mellan ursprung genom att utföra följande steg:

1. I menyn väljer du **CORS**.
1. Ange `https://your-tenant-name.b2clogin.com`för **tillåtna ursprung**. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com`. Du måste använda små bokstäver när du anger ditt klient namn.
1. För **tillåtna metoder**väljer du både `GET` och `OPTIONS`.
1. För **tillåtna huvuden**anger du en asterisk (*).
1. För **exponerade rubriker**anger du en asterisk (*).
1. Ange 200 för **högsta ålder**.
1. Klicka på **Save** (Spara).

## <a name="test-cors"></a>Testa CORS

Verifiera att du är redo genom att utföra följande steg:

1. Gå till [www.test-CORS.org](https://www.test-cors.org/) -webbplatsen och klistra in webb adressen i rutan **Fjärradress** .
1. Klicka på **skicka begäran**.
    Kontrol lera att [CORS-inställningarna](#configure-cors) är korrekta om du får ett fel meddelande. Du kan också behöva rensa webbläsarens cacheminne eller öppna en privat webbläsarsession genom att trycka på CTRL + SKIFT + P.

## <a name="modify-the-extensions-file"></a>Ändra tilläggs filen

Om du vill konfigurera UI-anpassning kopierar du **ContentDefinition** och dess underordnade element från bas filen till tilläggs filen.

1. Öppna bas filen för din princip. Till exempel <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> . Detta är en av de principfiler som ingår i Start paketet för den anpassade principen, som du bör ha fått i förutsättningen, [Kom igång med anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Sök efter och kopiera hela innehållet i **ContentDefinitions** -elementet.
1. Öppna tilläggs filen. Till exempel *TrustFrameworkExtensions. XML*. Sök efter **BuildingBlocks** -elementet. Om elementet inte finns lägger du till det.
1. Klistra in hela innehållet i **ContentDefinitions** -elementet som du kopierade som ett underordnat objekt till **BuildingBlocks** -elementet.
1. Sök efter det **ContentDefinition** -element som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.
1. Ändra värdet för **LoadUri** till URL: en för HTML-filen som du laddade upp till lagringen. Till exempel `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Den anpassade principen bör se ut så här:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Spara tilläggs filen.

## <a name="upload-your-updated-custom-policy"></a>Ladda upp en uppdaterad anpassad princip

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **ramverk för identitets upplevelse**.
1. Klicka på **alla principer**.
1. Klicka på **Ladda upp princip**.
1. Ladda upp tilläggs filen som du har ändrat tidigare.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av **Kör nu**

1. På sidan **Azure AD B2C** går du till **alla principer**.
1. Välj den anpassade princip som du överförde och klicka på knappen **Kör nu** .
1. Du bör kunna registrera dig med hjälp av en e-postadress.

## <a name="reference"></a>Referens

### <a name="sample-templates"></a>Exempelmallar
Du hittar exempel på mallar för anpassning av användar gränssnittet här:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Mappen sample_templates/Wingtip innehåller följande HTML-filer:

| HTML5-mall | Beskrivning |
|----------------|-------------|
| *phonefactor. html* | Använd den här filen som mall för en Multi-Factor Authentication-sida. |
| *resetpassword.html* | Använd den här filen som en mall för en glömt lösen ords sida. |
| *selfasserted.html* | Använd den här filen som mall för registrerings sidan för ett socialt konto, en registrerings sida för lokalt konto eller en inloggnings sida för lokalt konto. |
| *Unified. html* | Använd den här filen som mall för en enhetlig registrerings-eller inloggnings sida. |
| *updateprofile.html* | Använd den här filen som mall för en profil uppdaterings sida. |

Här följer stegen för att använda exemplet:

1. Klona lagrings platsen på den lokala datorn. Välj en mall-mapp under sample_templates. Du kan använda `wingtip` eller `contoso`.
1. Ladda upp alla filer under `css`, `fonts`och `images` mappar till Blob Storage enligt beskrivningen i föregående avsnitt.
1. Öppna sedan varje \*. html-fil i roten för antingen `wingtip` eller `contoso` (det du valde i det första steget) och Ersätt alla förekomster av "http://localhost" med URL: erna för de CSS-, bilder-och fonts-filer som du laddade upp i steg 2.
1. Spara \*. HTML-filerna och överför dem till Blob Storage.
1. Ändra nu tilläggs filen som nämnts tidigare i [ändra tilläggs filen](#modify-the-extensions-file).
1. Om du ser saknade teckensnitt, bilder eller CSS, kontrollerar du referenserna i tillägg-principen och \*. html-filer.

### <a name="content-definition-ids"></a>ID för innehålls definition

I avsnittet Ändra din registrering eller inloggning i anpassad princip konfigurerade du innehålls definitionen för `api.idpselections`. En fullständig uppsättning innehålls Definitions-ID: n som identifieras av Azure AD B2C Identity Experience Framework och deras beskrivningar finns i följande tabell:

| ID för innehålls definition | Beskrivning |
|-----------------------|-------------|
| *api.error* | **Felsida**. Den här sidan visas när ett undantag eller ett fel påträffas. |
| *API. idpselections* | **Sidan Val av identitets leverantör**. Den här sidan innehåller en lista över identitets leverantörer som användaren kan välja från vid inloggning. Dessa alternativ är antingen företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| *api.idpselections.signup* | **Val av identitets leverantör för registrering**. Den här sidan innehåller en lista över identitets leverantörer som användaren kan välja från vid registrering. Dessa alternativ är antingen företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| *api.localaccountpasswordreset* | **Sidan glömt lösen ord**. Den här sidan innehåller ett formulär som användaren måste utföra för att initiera en lösen ords återställning.  |
| *api.localaccountsignin* | **Inloggnings sida för lokalt konto**. Den här sidan innehåller ett inloggnings formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla text rutorna text rutor och lösen ord. |
| *api.localaccountsignup* | **Registrerings sida för lokalt konto**. Den här sidan innehåller ett registrerings formulär för registrering av ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, till exempel en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. |
| *API. phonefactor* | **Sidan Multi-Factor Authentication**. På den här sidan kan användarna verifiera sina telefonnummer (med hjälp av text eller röst) under registreringen eller inloggningen. |
| *api.selfasserted* | **Registrerings sida för socialt konto**. Den här sidan innehåller ett registrerings formulär som användarna måste slutföra när de registrerar sig genom att använda ett befintligt konto från en social identitetsprovider, till exempel Facebook eller Google +. Den här sidan påminner om den föregående registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| *api.selfasserted.profileupdate* | **Sidan profil uppdatering**. Den här sidan innehåller ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| *api.signuporsignin* | **Enhetlig registrerings-eller inloggnings sida**. Den här sidan hanterar både inloggnings-och inloggnings användare, som kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton.  |

## <a name="next-steps"></a>Nästa steg

Mer information om GRÄNSSNITTs element som kan anpassas finns i [referens guide för anpassning av användar flöden för användar flöden](active-directory-b2c-reference-ui-customization.md).
