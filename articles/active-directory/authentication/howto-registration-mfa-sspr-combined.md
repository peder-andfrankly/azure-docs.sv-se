---
title: Kom igång med kombinerad registrerings Azure Active Directory
description: Aktivera kombinerat Azure AD-Multi-Factor Authentication och registrering av lösen ords återställning via självbetjäning (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2bd3f61ffc07881ed8e502788b11fc0f435735b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847362"
---
# <a name="enable-combined-security-information-registration-preview"></a>Aktivera kombinerad säkerhets informations registrering (för hands version)

Innan du aktiverar den nya upplevelsen kan du läsa artikeln [kombinerad säkerhets informations registrering (för hands version)](concept-registration-mfa-sspr-combined.md) för att se till att du förstår funktionerna och effekterna av den här funktionen.

![Kombinerad säkerhets informations registrering förbättrad upplevelse](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinerad säkerhets informations registrering för Azure Multi-Factor Authentication och Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord är en offentlig förhands gransknings funktion i Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organisationer som har aktiverat den tidigare förhands granskningen för registrering och hantering av säkerhets information bör utföra stegen nedan för att aktivera den förbättrade för hands versionen. För organisationer som inte har någon växel, den 8 oktober 2019, kommer Microsoft att byta användare av den tidigare för hands versionen för att registrera och hantera säkerhets information till den förbättrade upplevelsen. 
> 
> Om du inte har aktiverat någon version av för hands versionen kommer din organisation inte att påverkas.

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Utför de här stegen för att aktivera kombinerad registrering:

1. Logga in på Azure Portal som en användar administratör eller global administratör.
2. Gå till **Azure Active Directory** > **användar inställningar** > **Hantera inställningar för för hands versions funktionerna i åtkomst panelen**.
3. Under **användare kan använda för hands versions funktioner för att registrera och hantera säkerhets information – uppdatera**, välja att aktivera för en **vald** grupp av användare eller för **alla** användare.

   ![Aktivera den kombinerade för hands versionen av säkerhets information för alla användare](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Från och med mars 2019 är Telefonsamtals alternativen inte tillgängliga för att Multi-Factor Authentication och SSPR-användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtals alternativen är fortfarande tillgängliga för användare i betalda Azure AD-klienter.

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sina telefonnummer eller mobilappar via den nya upplevelsen använda dem för Multi-Factor Authentication och SSPR, om dessa metoder är aktiverade i Multi-Factor Authentication och SSPR rikt. Om du sedan inaktiverar den här funktionen måste användare som går till den tidigare SSPR-registrerings sidan på `https://aka.ms/ssprsetup` utföra Multi-Factor Authentication innan de kan komma åt sidan.

Om du har konfigurerat plats till zon tilldelnings lista i Internet Explorer måste följande platser finnas i samma zon:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning är nu möjligt med användar åtgärder i princip för villkorlig åtkomst. Den här förhands gransknings funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade förhands granskningen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan vara aktive rad i organisationer där de vill att användarna ska kunna registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrott nätverks plats under en inledande registrering. Mer information om hur du skapar betrodda platser i villkorlig åtkomst finns i artikeln [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Följande princip gäller för alla valda användare, som försöker registrera sig med den kombinerade registrerings upplevelsen och blockerar åtkomsten om de inte ansluter från en plats som har marker ATS som ett betrott nätverk.

![Skapa en CA-princip för att kontrol lera registreringen av säkerhets information](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. I **Azure Portal**bläddrar du till **Azure Active Directory** > **villkorlig åtkomst**
1. Välj **Ny princip**
1. I namn anger du ett namn för den här principen. Till exempel **kombinerad säkerhets informations registrering på betrodda nätverk**
1. Under **tilldelningar**klickar du på **användare och grupper**och väljer de användare och grupper som du vill att den här principen ska tillämpas på

   > [!WARNING]
   > Användare måste aktive ras för för [hands versionen av den kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **molnappar eller åtgärder**väljer du **användar åtgärder**, markera **Registrera säkerhets information (förhands granskning)**
1. Under **villkor** > **platser**
   1. Konfigurera **Ja**
   1. Ta med **valfri plats**
   1. Undanta **alla betrodda platser**
   1. Klicka på **Done** på bladet platser
   1. Klicka på **färdig** på bladet villkor
1. Under **åtkomst kontroller** > **bevilja**
   1. Klicka på **blockera åtkomst**
   1. Klicka sedan på **Välj**
1. Ange att principen ska **aktive ras**
1. Klicka sedan på **skapa**

## <a name="next-steps"></a>Nästa steg

[Tvinga användare att registrera autentiseringsmetoder igen](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Tillgängliga metoder för Multi-Factor Authentication och SSPR](concept-authentication-methods.md)

[Konfigurera återställning av lösen ord för självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)

[Felsöka kombinerad säkerhets informations registrering](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md)
