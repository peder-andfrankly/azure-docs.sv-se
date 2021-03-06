---
title: Principer för Azure API Management-autentisering | Microsoft Docs
description: Lär dig mer om de autentiseringsprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514848"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Autentiseringsprinciper

-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en backend-tjänst med grundläggande autentisering.

-   [Autentisera med klient certifikat](api-management-authentication-policies.md#ClientCertificate) – autentisera med en backend-tjänst med hjälp av klient certifikat.

-   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) – autentisera med den [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) för den API Management tjänsten.

##  <a name="Basic"></a>Autentisera med Basic
 Använd den `authentication-basic` principen för att autentisera med en backend-tjänst med grundläggande autentisering. Den här principen anger att HTTP-Authorization-huvudet ska vara det värde som motsvarar de autentiseringsuppgifter som anges i principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exempel

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|autentisering – grundläggande|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|användarnamn|Anger användar namnet för den grundläggande autentiseringsuppgiften.|Ja|Gäller inte|
|lösenord|Anger lösen ordet för grundläggande autentiseringsuppgifter.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

##  <a name="ClientCertificate"></a>Autentisera med klient certifikat
 Använd `authentication-certificate`-principen för att autentisera med en backend-tjänst med hjälp av klient certifikat. Certifikatet måste [installeras i API Management](https://go.microsoft.com/fwlink/?LinkID=511599) först och identifieras av sitt tumavtryck.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exempel

I det här exemplet identifieras klient certifikatet med dess tumavtryck.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
I det här exemplet identifieras klient certifikatet med resurs namnet.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentisering-certifikat|Rot element.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|begäran|Tumavtryck för klient certifikatet.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Gäller inte|  
|certifikat-ID|Certifikat resursens namn.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar  

##  <a name="ManagedIdentity"></a>Autentisera med hanterad identitet  
 Använd `authentication-managed-identity`-principen för att autentisera med en backend-tjänst med hjälp av den hanterade identiteten för tjänsten API Management. Den här principen använder i princip den hanterade identiteten för att hämta en åtkomsttoken från Azure Active Directory för åtkomst till den angivna resursen. När token har hämtats ställer principen in värdet för token i `Authorization`-huvudet med `Bearer` schemat.
  
### <a name="policy-statement"></a>Princip kommentar  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exempel  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Använd hanterad identitet för att autentisera med en server dels tjänst
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Använd hanterad identitet i skicka begär ande princip
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentisering-hanterad-identitet |Rot element.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|Klusterresursen|nollängd. App-ID-URI för mål webb-API (säker resurs) i Azure Active Directory.|Ja|Gäller inte|  
|output-token-variabel-namn|nollängd. Namnet på den Sammanhangs variabel som kommer att ta emot token-värde som en objekt typ `string`. |Nej|Gäller inte|  
|Ignorera-fel|Booleskt. Om värdet är `true` fortsätter princip pipelinen att köras även om en åtkomsttoken inte har hämtats.|Nej|false|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](api-management-policy-reference.md) för en fullständig lista över princip satser och deras inställningar
+ [Princip exempel](policy-samples.md)
