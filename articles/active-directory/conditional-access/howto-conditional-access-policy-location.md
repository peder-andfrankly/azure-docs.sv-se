---
title: Villkorlig åtkomst – blockera åtkomst efter plats – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att blockera åtkomst till resurser via IP-adress
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26299ca12071b521fc825a0b1ab1425f24af67e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803605"
---
# <a name="conditional-access-block-access-by-location"></a>Villkorlig åtkomst: blockera åtkomst efter plats

Med plats villkoret i villkorlig åtkomst kan du styra åtkomsten till dina molnappar baserat på användarens nätverks plats. Plats villkoret används ofta för att blockera åtkomst från länder där organisationen vet att trafiken inte kommer från.

## <a name="define-locations"></a>Definiera platser

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **säkerhet** > **villkorlig åtkomst**.
1. Välj **ny plats**.
1. Namnge din plats.
1. Välj **IP-intervall** om du känner till de enskilda externa IPv4-adressintervall som utgör den platsen eller **länder/regioner**.
   1. Ange **IP-intervall** eller Välj **land/region** för den plats som du anger.
      * Om du väljer länder/regioner kan du välja att inkludera okända områden.
1. Välj **Spara**

Mer information om plats villkoret i villkorlig åtkomst finns i artikeln, [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory** > **säkerhet** > **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar**väljer **du användare och grupper**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Välj **Done** (Klar).
1. Under **molnappar eller åtgärder** > **inkluderar**väljer du **alla molnappar**och väljer sedan **Slutför**.
1. Under **villkor** > **plats**.
   1. **Konfigurera** till **Ja**
   1. **Inkludera** utvalda **valda platser**
   1. Välj den blockerade plats som du har skapat för din organisation.
   1. Klicka på **välj** > **gjorda** > har **slutförts**.
1. Under **åtkomst kontroller** > **block**och välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
