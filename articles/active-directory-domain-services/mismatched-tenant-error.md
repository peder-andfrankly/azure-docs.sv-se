---
title: Åtgärda felmatchade katalog fel i Azure AD Domain Services | Microsoft Docs
description: Lär dig mer om ett fel som inte matchar katalogen och hur du löser det i Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 0d96fd4c435251fb4dd604351e6efeea8bde8353
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704550"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Lös felmatchade katalog fel för befintliga Azure AD Domain Services hanterade domäner

Om en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän visar ett fel som inte matchar klient organisationen kan du inte administrera den hanterade domänen förrän den är löst. Det här felet uppstår om det underliggande virtuella Azure-nätverket flyttas till en annan Azure AD-katalog.

I den här artikeln förklaras varför felet uppstår och hur du löser det.

## <a name="what-causes-this-error"></a>Vad orsakar det här felet?

Ett felmatchat katalog fel inträffar när en Azure AD DS-hanterad domän och ett virtuellt nätverk tillhör två olika Azure AD-klienter. Du kan till exempel ha en Azure AD DS-hanterad domän med namnet *aadds.contoso.com* som körs i Contosos Azure AD-klient. Men det virtuella Azure-nätverket för hanterad domän är en del av den Fabrikam Azure AD-klienten.

Azure använder rollbaserad åtkomst kontroll (RBAC) för att begränsa åtkomsten till resurser. När du aktiverar Azure AD DS i en Azure AD-klient synkroniseras inloggnings-hashar till den hanterade domänen. Den här åtgärden kräver att du är innehavaradministratör för Azure AD-katalogen och att åtkomsten till autentiseringsuppgifterna måste kontrol leras. Om du vill distribuera resurser till ett virtuellt Azure-nätverk och kontrol lera trafik måste du ha administratörs behörighet för det virtuella nätverk där du distribuerar Azure AD DS.

För att RBAC ska fungera konsekvent och säker åtkomst till alla resurser som Azure AD DS använder, måste den hanterade domänen och det virtuella nätverket tillhöra samma Azure AD-klient.

Följande regler gäller i Resource Manager-miljön:

- En Azure AD-katalog kan ha flera Azure-prenumerationer.
- En Azure-prenumeration kan ha flera resurser, till exempel virtuella nätverk.
- En enda Azure AD Domain Services hanterad domän är aktive rad för en Azure AD-katalog.
- En Azure AD Domain Services hanterad domän kan aktive ras på ett virtuellt nätverk som tillhör någon av Azure-prenumerationerna inom samma Azure AD-klient.

### <a name="valid-configuration"></a>Giltig konfiguration

I följande exempel distributions scenario är contoso Azure AD DS-hanterad domän aktive rad i Contoso Azure AD-klienten. Den hanterade domänen distribueras i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av contoso Azure AD-klienten. Både den hanterade domänen och det virtuella nätverket tillhör samma Azure AD-klient. Den här exempel konfigurationen är giltig och stöds fullt ut.

![Giltig konfiguration av Azure AD DS-klient med den hanterade domänen och den virtuella nätverks delen av samma Azure AD-klient](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Felaktig matchning av klient konfiguration

I det här exempel distributions scenariot är contoso Azure AD DS-hanterad domän aktiverat i Contoso Azure AD-klienten. Den hanterade domänen distribueras dock i ett virtuellt nätverk som tillhör en Azure-prenumeration som ägs av den Fabrikam Azure AD-klienten. Den hanterade domänen och det virtuella nätverket tillhör två olika Azure AD-klienter. Den här exempel konfigurationen är en felaktig matchning av klienten och stöds inte. Det virtuella nätverket måste flyttas till samma Azure AD-klient som den hanterade domänen.

![Felaktig matchning av klient konfiguration](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Lös fel vid fel matchning av klient organisations fel

Följande två alternativ löser det felmatchade katalog felet:

* [Ta bort den hanterade Azure AD DS-domänen](delete-aadds.md) från din befintliga Azure AD-katalog. [Skapa en ersättnings Azure AD DS-hanterad domän](tutorial-create-instance.md) i samma Azure AD-katalog som det virtuella nätverk som du vill använda. När du är klar ansluter du alla datorer som tidigare var anslutna till den borttagna domänen till den återskapade hanterade domänen.
* [Flytta Azure-prenumerationen](../billing/billing-subscription-transfer.md) som innehåller det virtuella nätverket till samma Azure AD-katalog som den hanterade domänen för Azure AD DS.

## <a name="next-steps"></a>Nästa steg

Mer information om fel sökning av problem med Azure AD DS finns i [fel söknings guiden](troubleshoot.md).
