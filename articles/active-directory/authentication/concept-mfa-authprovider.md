---
title: Azure Multi-Factor Auth-provider – Azure Active Directory
description: När ska du använda en auth-Provider med Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ac23bbb0465be2f15e0a35bb4fd7c331e83988
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848722"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>När du ska använda en Azure Multi-Factor Authentication-provider

Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](howto-mfa-mfasettings.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

En Azure Multi-Factor Auth-provider används för att dra nytta av funktioner som tillhandahålls av Azure Multi-Factor Authentication för användare som **inte har licenser**.

> [!NOTE]
> Från och med den 1 september 2018 kan nya auth providers inte längre skapas. Befintliga auth-providers kan fortsätta att användas och uppdateras, men migreringen är inte längre möjlig. Multi-Factor Authentication är fortfarande tillgängligt som en funktion i Azure AD Premium licenser.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Varningar som rör Azure MFA SDK

Observera att SDK: n är inaktuell och fortsätter att fungera fram till 14 november 2018. Efter det misslyckas anrop till SDK.

## <a name="what-is-an-mfa-provider"></a>Vad är en MFA-provider?

Det finns två typer av auth-providers, och skillnaden är ungefär hur din Azure-prenumeration debiteras. Med alternativet per autentisering räknas antalet autentiseringar som utförs mot din klientorganisation under en månad. Det här alternativet är bäst om du har ett antal användare som bara autentiserar sig ibland. Med alternativet per användare räknas hur många användare i din klientorganisation som utför en tvåstegsverifiering under en månad. Det här alternativet är bäst om du har några användare med licenser men behöver utöka MFA till fler användare utanför licensgränsen.

## <a name="manage-your-mfa-provider"></a>Hantera din MFA-provider

Du kan inte ändra användningsmodellen (per aktiverad användare eller per autentisering) efter att en MFA-provider har skapats.

Om du har köpt tillräckligt många licenser för att alla användare som är aktiverade för MFA, kan du ta bort MFA-providern helt.

Om MFA-providern inte är kopplad till en Azure AD-klientorganisation, eller om du kopplar den nya MFA-providern till en annan Azure AD-klientorganisation, överförs inte användarinställningar eller konfigurationsalternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av de aktiverings referenser som genereras via MFA-providern. Om du återaktiverar MFA-servrarna för att länka dem till MFA-providern påverkas inte autentiseringen av telefonsamtal och textmeddelande, men meddelanden om mobilappar upphör att fungera för alla användare tills de återaktiverar mobilappen.

### <a name="removing-an-authentication-provider"></a>Ta bort en autentiseringsprovider

> [!CAUTION]
> Det finns ingen bekräftelse när du tar bort en autentiseringsprovider. Att välja **ta bort** är en permanent process.

Du hittar autentiseringsproviders i **Azure Portal** > **Azure Active Directory** > **MFA** > - **leverantörer**. Klicka på listade leverantörer för att se information och konfigurationer som är kopplade till den providern.

Innan du tar bort en autentiseringsprovider bör du anteckna alla anpassade inställningar som kon figurer ATS i din Provider. Bestäm vilka inställningar som måste migreras till allmänna MFA-inställningar från din Provider och Slutför migreringen av dessa inställningar. 

Azure MFA-servrar som är länkade till providrar måste återaktiveras med hjälp av autentiseringsuppgifter som genereras under **Azure Portal** > **Azure Active Directory** > **MFA** > **Server inställningar**. Innan du återaktiverar måste följande filer tas bort från `\Program Files\Multi-Factor Authentication Server\Data\` Directory på Azure MFA-servrar i din miljö:

- caCert
- certifikatet
- groupCACert
- groupKey
- Namn
- Licens nyckeln
- pkey

![Ta bort en autentiseringsprovider från Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

När du har bekräftat att alla inställningar har migrerats kan du bläddra till **Azure Portal** > **Azure Active Directory** > **MFA** > **providers** och välja ellipserna **...** och välj **ta bort**.

> [!WARNING]
> Om du tar bort en autentiseringsprovider tas all rapporterings information som är associerad med providern bort. Du kanske vill spara aktivitets rapporter innan du tar bort din Provider.

> [!NOTE]
> Användare med äldre versioner av Microsoft Authenticator-appen och Azure MFA-servern kan behöva registrera appen igen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för Multi-Factor Authentication](howto-mfa-mfasettings.md)
