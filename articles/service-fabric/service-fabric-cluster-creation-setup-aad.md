---
title: Konfigurera Azure Active Directory för Service Fabric klientautentisering | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) för att autentisera klienter för Service Fabric kluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 77814d04daca0ebb649ffa2e8ff46becddec4f0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901509"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

För kluster som körs på Azure rekommenderas Azure Active Directory (Azure AD) för att skydda åtkomsten till hanterings slut punkter. Den här artikeln beskriver hur du konfigurerar Azure AD för att autentisera klienter för ett Service Fabric kluster.

I den här artikeln kommer termen "program" att användas för att referera till [Azure Active Directory program](../active-directory/develop/developer-glossary.md#client-application), inte Service Fabric program. skillnaden kommer att göras där det behövs. Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program.

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer][service-fabric-visualizing-your-cluster] och [Visual Studio][service-fabric-manage-application-in-visual-studio]. Därför kan du skapa två Azure AD-program för att kontrol lera åtkomsten till klustret: ett webb program och ett internt program. När programmen har skapats tilldelar du användare till skrivskyddade och administratörs roller.

> [!NOTE]
> I Linux måste du utföra följande steg innan du skapar klustret. I Windows har du också möjlighet att [Konfigurera Azure AD-autentisering för ett befintligt kluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

## <a name="prerequisites"></a>Krav
I den här artikeln förutsätter vi att du redan har skapat en klientorganisation. Om du inte har gjort det börjar du med att läsa avsnittet om [hur du skaffar en Azure Active Directory-klientorganisation][active-directory-howto-tenant].

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av de steg som används för att konfigurera Azure AD med Service Fabric-kluster.

1. [Klona lagrings platsen](https://github.com/Azure-Samples/service-fabric-aad-helpers) till din dator.
2. [Se till att du har alla krav](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) för skript som är installerade.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Skapa Azure AD-program och tilldela användare till roller

Vi använder skripten för att skapa två Azure AD-program för att kontrol lera åtkomsten till klustret: ett webb program och ett internt program. När du har skapat program som representerar klustret skapar du användare för de [roller som stöds av Service Fabric](service-fabric-cluster-security-roles.md): skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar.  Ange även användarnamn och lösenord för användarna. Exempel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure Kina och Azure Tyskland) bör du även ange parametern `-Location`.

Du kan hitta ditt *TenantId* genom att köra PowerShell-kommandot `Get-AzureSubscription`. Om du kör det här kommandot visas TenantId för varje prenumeration.

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsett att göra det enklare att mappa Azure AD-artefakter till det Service Fabric-kluster som de används med.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slut punkten som Service Fabric Explorer slut punkt för klustret. Om du skapar Azure AD-program som ska representera ett befintligt kluster kontrollerar du att URL: en matchar det befintliga klustrets slut punkt. Om du skapar program för ett nytt kluster bör du planera slut punkten som klustret kommer att ha och se till att inte använda slut punkten för ett befintligt kluster. Som standard är Service Fabric Explorer slut punkten:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klientorganisationen. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. Om du tittar på klientorganisationens program i [Azure-portalen][azure-portal] bör du se två nya poster:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Skriptet skriver ut JSON-filen som krävs av Azure Resource Manager-mallen när du [skapar ditt AAD-aktiverade kluster](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), så det är en bra idé att låta PowerShell-fönstret vara öppet.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Fel söknings hjälp vid installation av Azure Active Directory
Det kan vara svårt att konfigurera Azure AD och använda det, så här är några pekare om vad du kan göra för att felsöka problemet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer du blir ombedd att välja ett certifikat
#### <a name="problem"></a>Problem
När du har loggat in på Azure AD i Service Fabric Explorer återgår webbläsaren till start sidan, men ett meddelande visas där du uppmanas att välja ett certifikat.

![Dialog rutan SFX-certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Orsak
Användaren har inte tilldelats någon roll i Azure AD-klustret. Azure AD-autentisering Miss lyckas därför på Service Fabric klustret. Service Fabric Explorer hamnar tillbaka till certifikatautentisering.

#### <a name="solution"></a>Lösning
Följ anvisningarna för att konfigurera Azure AD och tilldela användar roller. Vi rekommenderar också att du aktiverar "användar tilldelning krävs för att få åtkomst till appen" som `SetupApplications.ps1` gör.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Anslutning med PowerShell Miss lyckas med ett fel: "de angivna autentiseringsuppgifterna är ogiltiga"
#### <a name="problem"></a>Problem
När du använder PowerShell för att ansluta till klustret genom att använda säkerhets läget "AzureActiveDirectory" när du har loggat in på Azure AD, Miss lyckas anslutningen med ett fel: "de angivna autentiseringsuppgifterna är ogiltiga."

#### <a name="solution"></a>Lösning
Den här lösningen är samma som föregående.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer returnerar ett problem när du loggar in: "AADSTS50011"
#### <a name="problem"></a>Problem
När du försöker logga in på Azure AD i Service Fabric Explorer returnerar sidan ett fel: "AADSTS50011: svars adressen &lt;URL&gt; matchar inte svars adresserna som har kon figurer ATS för programmet: &lt;-GUID&gt;."

![Svars adressen för SFX matchar inte][sfx-reply-address-not-match]

#### <a name="reason"></a>Orsak
Kluster (webb) som representerar Service Fabric Explorer försöker autentisera mot Azure AD, och som en del av begäran tillhandahåller URL: en för omdirigerings RETUR. Men URL: en visas inte i listan Azure AD Application **svars-URL** .

#### <a name="solution"></a>Lösning
Välj Appregistreringar på AAD-sidan, Välj ditt kluster program och välj sedan knappen **svars-URL: er** . På sidan svars-URL: er lägger du till URL: en för Service Fabric Explorer i listan eller ersätter ett objekt i listan. När du är färdig sparar du ändringen.

![Webb program svars-URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ansluta klustret med hjälp av Azure AD-autentisering via PowerShell
Om du vill ansluta Service Fabric klustret använder du följande PowerShell-kommando exempel:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Mer information finns i [cmdleten Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan jag återanvända samma Azure AD-klient i flera kluster?
Ja. Kom ihåg att lägga till URL: en för Service Fabric Explorer till ditt kluster (webb program). Annars fungerar inte Service Fabric Explorer.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Varför behöver jag fortfarande ett Server certifikat när Azure AD är aktiverat?
FabricClient och FabricGateway utför en ömsesidig autentisering. Vid Azure AD-autentisering tillhandahåller Azure AD-integrering en klient identitet för servern och Server certifikatet används av klienten för att verifiera serverns identitet. Mer information om Service Fabric certifikat finns i avsnittet om [X. 509-certifikat och Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat Azure Active Directory program och ställt in roller för användare [konfigurerar du och distribuerar ett kluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
