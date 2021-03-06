---
title: Principer för begränsning av Azure API Management åtkomst | Microsoft Docs
description: Lär dig mer om principerna för åtkomst begränsning som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 3201edd3b90d6db1393286db688b24065ea8dc6b
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273531"
---
# <a name="api-management-access-restriction-policies"></a>Principer för begränsning av API Management åtkomst

Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Principer för begränsning av åtkomst

-   [Kontrol lera http-huvud](api-management-access-restriction-policies.md#CheckHTTPHeader) -framtvingar förekomst och/eller värde för ett HTTP-huvud.
-   [Begränsa anrops frekvensen av prenumeration](api-management-access-restriction-policies.md#LimitCallRate) – förhindrar att API-användnings toppar begränsas genom att begränsa anrops frekvensen, per prenumeration.
-   [Begränsa anrops frekvensen per nyckel](#LimitCallRateByKey) – förhindrar att API-användnings toppar begränsas genom att begränsa anrops frekvensen, per nyckel.
-   [Begränsa anroparen IP](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar) anrop från vissa IP-adresser och/eller adress intervall.
-   [Ange användnings kvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – gör att du kan tvinga en förnyad eller livs längds samtals volym och/eller bandbredds kvot, per prenumeration.
-   [Ange användnings kvot per nyckel](#SetUsageQuotaByKey) – gör att du kan framtvinga en förnyad eller livs längds samtals volym och/eller bandbredds kvot, per nyckel.
-   [Verifiera att JWT](api-management-access-restriction-policies.md#ValidateJWT) -framtvingar förekomst och giltighet av ett JWT extraheras från antingen ett angivet http-huvud eller en angiven frågeparameter.

> [!TIP]
> Du kan använda principer för begränsning av åtkomst i olika omfång för olika användnings områden. Du kan till exempel skydda hela API: t med AAD-autentisering genom att `validate-jwt` tillämpa principen på API-nivå eller så kan du använda den på API-åtgärds `claims` nivå och använda för mer detaljerad kontroll.

## <a name="CheckHTTPHeader"></a>Kontrol lera HTTP-huvud

`check-header` Använd principen för att genomdriva att en begäran har ett angivet http-huvud. Du kan också kontrol lera om det finns ett angivet värde för huvudet eller söka efter ett intervall med tillåtna värden. Om kontrollen Miss lyckas avslutar principen bearbetning av begär Anden och returnerar HTTP-statuskoden och det fel meddelande som anges av principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Exempel

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Element

| Name         | Beskrivning                                                                                                                                   | Obligatorisk |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Check-sidhuvud | Rot element.                                                                                                                                 | Ja      |
| value        | Tillåtet värde för HTTP-huvud. När flera värde element anges, betraktas kontrollen som lyckad om något av värdena är en matchning. | Nej       |

### <a name="attributes"></a>Attribut

| Name                       | Beskrivning                                                                                                                                                            | Obligatorisk | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| misslyckades-kontrol lera fel meddelande | Fel meddelande att returnera i HTTP-svarets text om huvudet inte finns eller har ett ogiltigt värde. Det här meddelandet måste ha specialtecken som kan undantas korrekt. | Ja      | Gäller inte     |
| misslyckades-check-httpcode      | HTTP-statuskod att returnera om huvudet inte finns eller har ett ogiltigt värde.                                                                                        | Ja      | Gäller inte     |
| rubrik-namn                | Namnet på det HTTP-huvud som ska kontrol leras.                                                                                                                                  | Ja      | Gäller inte     |
| Ignorera Skift läge                | Kan anges till true eller false. Om värdet är sant ignoreras när värdet för huvudet jämförs med en uppsättning acceptabla värden.                                    | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående

-   **Princip omfattningar:** alla omfattningar

## <a name="LimitCallRate"></a>Begränsa anrops frekvens per prenumeration

`rate-limit` Principen förhindrar att API-användningen upprättar per prenumeration genom att begränsa anrops hastigheten till ett angivet antal per angiven tids period. När den här principen utlöses får anroparen en `429 Too Many Requests` svars status kod.

> [!IMPORTANT]
> Den här principen kan bara användas en gång per princip dokument.
>
> Det går inte att använda [princip uttryck](api-management-policy-expressions.md) i någon av principens attribut för den här principen.

> [!CAUTION]
> På grund av den distribuerade typen av begränsnings arkitektur är hastighets begränsningen aldrig helt korrekt. Skillnaden mellan konfigurerade och det verkliga antalet tillåtna begär Anden varierar beroende på volym och hastighet för begäran, Server svars tid och andra faktorer.

### <a name="policy-statement"></a>Princip kommentar

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Name      | Beskrivning                                                                                                                                                                                                                                                                                              | Obligatorisk |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| hastighets begränsning | Rot element.                                                                                                                                                                                                                                                                                            | Ja      |
| api       | Lägg till en eller flera av de här elementen för att införa en gräns för anrops frekvensen för API: er i produkten. Hastighets begränsningar för produkt-och API-anrop tillämpas oberoende av varandra. API: t kan refereras antingen `name` via `id`eller. Om båda attributen anges `id` används och `name` kommer att ignoreras.                    | Nej       |
| operation | Lägg till en eller flera av de här elementen för att införa en gräns för anrops frekvensen för åtgärder inom ett API. Hastighets begränsningar för produkt, API och åtgärd tillämpas oberoende av varandra. Åtgärden kan refereras antingen via `name` eller. `id` Om båda attributen anges `id` används och `name` kommer att ignoreras. | Nej       |

### <a name="attributes"></a>Attribut

| Name           | Beskrivning                                                                                           | Obligatorisk | Standard |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Namnet på det API som hastighets begränsningen ska tillämpas för.                                                | Ja      | Gäller inte     |
| anrop          | Maximalt antal anrop som tillåts under det tidsintervall som anges i `renewal-period`. | Ja      | Gäller inte     |
| förnyelse-period | Tids perioden i sekunder efter vilken kvoten återställs.                                              | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** produkt, API, åtgärd

## <a name="LimitCallRateByKey"></a>Begränsa anrops frekvens per nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig på **förbruknings** nivån för API Management.

`rate-limit-by-key` Principen förhindrar att API-användningen upprättar per nyckel genom att begränsa anrops hastigheten till ett angivet antal per angiven tids period. Nyckeln kan ha ett godtyckligt sträng värde och anges vanligt vis med ett princip uttryck. Valfritt öknings villkor kan läggas till för att ange vilka begär Anden som ska räknas mot gränsen. När den här principen utlöses får anroparen en `429 Too Many Requests` svars status kod.

Mer information och exempel på den här principen finns i [Avancerad begränsning av förfrågningar med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> På grund av den distribuerade typen av begränsnings arkitektur är hastighets begränsningen aldrig helt korrekt. Skillnaden mellan konfigurerade och det verkliga antalet tillåtna begär Anden varierar beroende på volym och hastighet för begäran, Server svars tid och andra faktorer.

### <a name="policy-statement"></a>Princip kommentar

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exempel

I följande exempel anges frekvens gränsen av IP-adressen för anroparen.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Name              | Beskrivning   | Obligatorisk |
| ----------------- | ------------- | -------- |
| hastighets begränsning för nyckel | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Name                | Beskrivning                                                                                           | Obligatorisk | Standard |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| anrop               | Maximalt antal anrop som tillåts under det tidsintervall som anges i `renewal-period`. | Ja      | Gäller inte     |
| räknare-nyckel         | Den nyckel som ska användas för frekvens begränsnings principen.                                                             | Ja      | Gäller inte     |
| Increment-Condition | Det booleska uttryck som anger om begäran ska räknas mot kvoten (`true`).        | Nej       | Gäller inte     |
| förnyelse-period      | Tids perioden i sekunder efter vilken kvoten återställs.                                              | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

## <a name="RestrictCallerIPs"></a>Begränsa anropare IP-adresser

`ip-filter` Princip filter (tillåter/nekar)-anrop från vissa IP-adresser och/eller adress intervall.

### <a name="policy-statement"></a>Princip kommentar

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exempel

I följande exempel tillåter principen bara begär Anden som kommer från den enskilda IP-adressen eller ett intervall med IP-adresser som anges

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Element

| Name                                      | Beskrivning                                         | Obligatorisk                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| IP-filter                                 | Rot element.                                       | Ja                                                            |
| Adress                                   | Anger en enskild IP-adress som du vill filtrera efter.   | Minst ett `address` eller `address-range` -element krävs. |
| adress – intervall från = "adress" till = "adress" | Anger ett intervall med IP-adresser som ska filtreras. | Minst ett `address` eller `address-range` -element krävs. |

### <a name="attributes"></a>Attribut

| Name                                      | Beskrivning                                                                                 | Obligatorisk                                           | Standard |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adress – intervall från = "adress" till = "adress" | Ett intervall med IP-adresser som tillåter eller nekar åtkomst för.                                        | Krävs när `address-range` elementet används. | Gäller inte     |
| ip-filter action="allow &#124; forbid"    | Anger om anrop ska tillåtas eller inte för de angivna IP-adresserna och intervallen. | Ja                                                | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande
-   **Princip omfattningar:** alla omfattningar

## <a name="SetUsageQuota"></a>Ange användnings kvot per prenumeration

`quota` Principen tillämpar en förnyad eller livs längd för anrops volymer och/eller bandbredds kvoten per prenumeration.

> [!IMPORTANT]
> Den här principen kan bara användas en gång per princip dokument.
>
> Det går inte att använda [princip uttryck](api-management-policy-expressions.md) i någon av principens attribut för den här principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Name      | Beskrivning                                                                                                                                                                                                                                                                                  | Obligatorisk |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvot     | Rot element.                                                                                                                                                                                                                                                                                | Ja      |
| api       | Lägg till en eller flera av de här elementen för att införa anrops kvot för API: er i produkten. Kvoten för produkt-och API-anrop tillämpas oberoende av varandra. API: t kan refereras antingen `name` via `id`eller. Om båda attributen anges `id` används och `name` kommer att ignoreras.                    | Nej       |
| operation | Lägg till en eller flera av de här elementen för att införa anrops kvoter för åtgärder inom ett API. Kvoter för produkt, API och åtgärds samtal tillämpas oberoende av varandra. Åtgärden kan refereras antingen via `name` eller. `id` Om båda attributen anges `id` används och `name` kommer att ignoreras. | Nej       |

### <a name="attributes"></a>Attribut

| Name           | Beskrivning                                                                                               | Obligatorisk                                                         | Standard |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Namnet på det API eller den åtgärd som kvoten gäller.                                             | Ja                                                              | Gäller inte     |
| bandbredd      | Det maximala totala antalet kilobyte som tillåts under det tidsintervall som anges i `renewal-period`. | Antingen `calls` ,`bandwidth`eller båda tillsammans måste anges. | Gäller inte     |
| anrop          | Maximalt antal anrop som tillåts under det tidsintervall som anges i `renewal-period`.     | Antingen `calls` ,`bandwidth`eller båda tillsammans måste anges. | Gäller inte     |
| förnyelse-period | Tids perioden i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande
-   **Princip omfattningar:** produkt

## <a name="SetUsageQuotaByKey"></a>Ange användnings kvot per nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig på **förbruknings** nivån för API Management.

`quota-by-key` Principen tillämpar en förnyad eller livs längds anrops volym och/eller bandbredds kvot, per nyckel. Nyckeln kan ha ett godtyckligt sträng värde och anges vanligt vis med ett princip uttryck. Valfritt öknings villkor kan läggas till för att ange vilka begär Anden som ska räknas mot kvoten. Om flera principer ökar samma nyckel värde ökas det bara en gång per begäran. När anrops gränsen har nåtts får anroparen en `403 Forbidden` svars status kod.

Mer information och exempel på den här principen finns i [Avancerad begränsning av förfrågningar med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Princip kommentar

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exempel

I följande exempel anges kvoten av IP-adressen för anroparen.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Name  | Beskrivning   | Obligatorisk |
| ----- | ------------- | -------- |
| kvot | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Name                | Beskrivning                                                                                               | Obligatorisk                                                         | Standard |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandbredd           | Det maximala totala antalet kilobyte som tillåts under det tidsintervall som anges i `renewal-period`. | Antingen `calls` ,`bandwidth`eller båda tillsammans måste anges. | Gäller inte     |
| anrop               | Maximalt antal anrop som tillåts under det tidsintervall som anges i `renewal-period`.     | Antingen `calls` ,`bandwidth`eller båda tillsammans måste anges. | Gäller inte     |
| räknare-nyckel         | Den nyckel som ska användas för kvot principen.                                                                      | Ja                                                              | Gäller inte     |
| Increment-Condition | Det booleska uttryck som anger om begäran ska räknas mot kvoten (`true`)             | Nej                                                               | Gäller inte     |
| förnyelse-period      | Tids perioden i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande
-   **Princip omfattningar:** alla omfattningar

## <a name="ValidateJWT"></a>Verifiera JWT

`validate-jwt` Principen upprätthåller förekomsten och giltighets tiden för ett JWT som extraheras från antingen ett angivet http-huvud eller en angiven frågeparameter.

> [!IMPORTANT]
> Principen kräver att det `exp` registrerade anspråket ingår i JWT-token, `require-expiration-time` om inte attributet har angetts och `false`angetts till. `validate-jwt`
> Principen `validate-jwt` stöder algoritmer för HS256-och RS256-signering. För HS256 måste nyckeln anges i en infogad princip i principen i Base64-kodat format. För RS256 måste nyckeln tillhandahållas via en konfigurations slut punkt med öppen ID.
> `validate-jwt` Principen stöder token som krypteras med symmetriska nycklar med hjälp av följande krypteringsalgoritmer A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Princip kommentar

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Exempel

#### <a name="simple-token-validation"></a>Enkel verifiering av token

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Verifiering av Azure Active Directory-token

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Verifiering av Azure Active Directory B2C-token

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Auktorisera åtkomst till åtgärder baserat på token-anspråk

Det här exemplet visar hur du använder [validate JWT](api-management-access-restriction-policies.md#ValidateJWT) -principen för att auktorisera åtkomst till åtgärder baserat på värdet token för token.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Verifiering av Azure Mobile Services-token

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Element

| Element             | Beskrivning                                                                                                                                                                                                                                                                                                                                           | Obligatorisk |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Rot element.                                                                                                                                                                                                                                                                                                                                         | Ja      |
| mål grupper           | Innehåller en lista över acceptabla mål grupps anspråk som kan finnas i token. Om det finns flera mål grupps värden görs ett försök tills alla är uttömda (i vilket fall valideringen Miss lyckas) eller tills det lyckas. Minst en mål grupp måste anges.                                                                     | Nej       |
| utfärdare-signerings nycklar | En lista med base64-kodade säkerhets nycklar som används för att validera signerade tokens. Om det finns flera säkerhets nycklar, så provas varje nyckel tills antingen alla är förbrukade (i vilket fall valideringen Miss lyckas) eller tills en lyckad (användbart för token-förnyelse). Nyckel element har ett valfritt `id` attribut som används för att `kid` matcha anspråk.               | Nej       |
| dekryptering – nycklar     | En lista med base64-kodade nycklar som används för att dekryptera tokens. Om det finns flera säkerhets nycklar, så provas varje nyckel tills alla nycklar är uttömda (i vilket fall valideringen Miss lyckas) eller tills en nyckel lyckas. Nyckel element har ett valfritt `id` attribut som används för att `kid` matcha anspråk.                                                 | Nej       |
| utfärdare             | En lista över acceptabla objekt som utfärdade token. Om det finns flera Issuer-värden görs ett försök tills alla är uttömda (i vilket fall valideringen Miss lyckas) eller tills det lyckas.                                                                                                                                         | Nej       |
| OpenID – config       | Det element som används för att ange en kompatibel konfigurations slut punkt för öppen ID från vilken signerings nycklar och utfärdare kan hämtas.                                                                                                                                                                                                                        | Nej       |
| obligatoriska-anspråk     | Innehåller en lista över anspråk som förväntas finnas i token för att anses vara giltiga. Om attributet har angetts till `all` alla anspråks värden i principen måste de finnas i token för att verifieringen ska lyckas. `match` Om attributet har angetts till `any` minst ett anspråk måste det finnas i token för att verifieringen ska lyckas. `match` | Nej       |
| Zumo – huvud nyckel     | Huvud nyckel för token som utfärdats av Azure Mobile Services                                                                                                                                                                                                                                                                                                 | Nej       |

### <a name="attributes"></a>Attribut

| Name                            | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                            | Obligatorisk                                                                         | Standard                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| klock skevning                      | Intervall. Används för att ange den maximala förväntade tids skillnaden mellan system klockorna i token Issuer och API Management-instansen.                                                                                                                                                                                                                                                                                                               | Nej                                                                               | 0 sekunder                                                                         |
| failed-validation-error-message | Fel meddelande att returnera i HTTP-svarets text om JWT inte klarar verifieringen. Det här meddelandet måste ha specialtecken som kan undantas korrekt.                                                                                                                                                                                                                                                                                                 | Nej                                                                               | Standard fel meddelandet är beroende av verifierings problemet, till exempel "JWT saknas". |
| misslyckades – validering-httpcode      | HTTP-statuskod att returnera om JWT inte klarar verifieringen.                                                                                                                                                                                                                                                                                                                                                                                         | Nej                                                                               | 401                                                                               |
| rubrik-namn                     | Namnet på det HTTP-huvud som innehåller token.                                                                                                                                                                                                                                                                                                                                                                                                         | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| fråga-parameter-Name            | Namnet på frågeparametern som innehåller token.                                                                                                                                                                                                                                                                                                                                                                                                     | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| token-värde                     | Uttrycket returnerar en sträng som innehåller JWT-token                                                                                                                                                                                                                                                                                                                                                                                                     | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| id                              | Attributet i- elementetgör`kid` att du kan ange den sträng som ska matchas mot anspråk i token (om det finns) för att ta reda på vilken nyckel som ska användas för verifiering av signaturen. `key` `id`                                                                                                                                                                                                                                           | Nej                                                                               | Gäller inte                                                                               |
| matchning                           | `match` Attributet`claim` i elementet anger om alla anspråks värden i principen måste finnas i token för att verifieringen ska lyckas. Möjliga värden är:<br /><br /> - `all`-alla anspråks värden i principen måste finnas i token för att verifieringen ska lyckas.<br /><br /> - `any`-minst ett anspråks värde måste finnas i token för att verifieringen ska lyckas.                                                       | Nej                                                                               | all                                                                               |
| require-expiration-time         | Booleskt. Anger om ett förfallo datum måste anges i token.                                                                                                                                                                                                                                                                                                                                                                               | Nej                                                                               | true                                                                              |
| Kräv schema                  | Namnet på token-schemat, t. ex. "Bearer". När det här attributet anges ser principen till att det angivna schemat finns i värdet för Authorization-huvudet.                                                                                                                                                                                                                                                                                    | Nej                                                                               | Gäller inte                                                                               |
| Kräv-signerade-token           | Booleskt. Anger om en token måste signeras.                                                                                                                                                                                                                                                                                                                                                                                           | Nej                                                                               | true                                                                              |
| brytning                       | Nollängd. Anger en avgränsare (t. ex. ",") som ska användas för att extrahera en uppsättning värden från ett multi-Value-anspråk.                                                                                                                                                                                                                                                                                                                                          | Nej                                                                               | Gäller inte                                                                               |
| url                             | URL för konfiguration av öppen ID-konfiguration från där det går att hämta metadata för konfiguration av öppen ID. Svaret bör vara enligt de specifikationer som definierats på URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`en:. För Azure Active Directory använder du följande URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` ersätta din katalogs klient namn, t. `contoso.onmicrosoft.com`ex. | Ja                                                                              | Gäller inte                                                                               |
output-token-variable-name|Nollängd. Namnet på den Sammanhangs variabel som kommer att ta emot token- [`Jwt`](api-management-policy-expressions.md) värde som ett objekt av typen vid lyckad token-verifiering|Nej|Gäller inte

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande
-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API Management](api-management-howto-policies.md)
-   [Transformera API: er](transform-api.md)
-   [Princip referens](api-management-policy-reference.md) för en fullständig lista över princip satser och deras inställningar
-   [Princip exempel](policy-samples.md)
