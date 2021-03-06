---
title: Konfigurera Azure MFA NPS-tillägget – Azure Active Directory
description: När du har installerat NPS-tillägget använder du de här stegen för Avancerad konfiguration som IP-vit listning och UPN-ersättning.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ea5b4f52fc161cb8359ef56e76e0607459d6280
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848365"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Avancerade konfigurations alternativ för NPS-tillägget för Multi-Factor Authentication

Nätverks princip Server tillägget (NPS) utökar dina molnbaserade Azure Multi-Factor Authentication-funktioner till din lokala infrastruktur. Den här artikeln förutsätter att du redan har installerat tillägget och nu vill veta hur du anpassar tillägget efter behov. 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID

Eftersom NPS-tillägget ansluter till både dina lokala och molnbaserade kataloger kan du stöta på ett problem där dina lokala UPN-namn (User Principal Names) inte matchar namnen i molnet. Lös problemet genom att använda alternativa inloggnings-ID: n. 

I NPS-tillägget kan du ange ett Active Directory-attribut som ska användas i stället för UPN för Azure Multi-Factor Authentication. På så sätt kan du skydda dina lokala resurser med tvåstegsverifiering utan att ändra dina lokala UPN-ändringar. 

Om du vill konfigurera alternativa inloggnings-ID: n går du till `HKLM\SOFTWARE\Microsoft\AzureMfa` och redigerar följande register värden:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sträng | Tom | Ange namnet på Active Directory attributet som du vill använda i stället för UPN. Det här attributet används som AlternateLoginId-attribut. Om registervärdet är inställt på ett [giltigt Active Directory-attribut](https://msdn.microsoft.com/library/ms675090.aspx) (till exempel e-post eller DisplayName) används attributets värde i stället för användarens UPN för autentisering. Om registervärdet är tomt eller inte har kon figurer ATS så inaktive ras AlternateLoginId och användarens UPN används för autentisering. |
| LDAP_FORCE_GLOBAL_CATALOG | boolesk | Falskt | Använd den här flaggan om du vill framtvinga användning av global katalog för LDAP-sökningar när du söker efter AlternateLoginId. Konfigurera en domänkontrollant som global katalog, Lägg till attributet AlternateLoginId i den globala katalogen och aktivera sedan den här flaggan. <br><br> Om LDAP_LOOKUP_FORESTS har kon figurer ATS (inte tom) **tillämpas den här flaggan som sant**, oavsett värdet för register inställningen. I det här fallet kräver NPS-tillägget att den globala katalogen konfigureras med attributet AlternateLoginId för varje skog. |
| LDAP_LOOKUP_FORESTS | sträng | Tom | Ange en semikolonavgränsad lista över skogar att söka i. Till exempel *contoso. com; foobar. com*. Om registervärdet har kon figurer ATS söker NPS-tillägget iterativt i alla skogar i den ordning som de listades och returnerar det första lyckade AlternateLoginId-värdet. Om registervärdet inte har kon figurer ATS begränsas AlternateLoginId-sökningen till den aktuella domänen.|

Använd de rekommenderade stegen för [alternativa inloggnings-ID-fel](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)för att felsöka problem med alternativa inloggnings-ID.

## <a name="ip-exceptions"></a>IP-undantag

Om du behöver övervaka Server tillgänglighet, t. ex. om belastnings utjämning verifierar vilka servrar som körs innan du skickar arbets belastningar, vill du inte att dessa kontroller ska blockeras av verifierings begär Anden. Skapa i stället en lista med IP-adresser som du vet används av tjänst konton och inaktivera Multi-Factor Authentication krav för listan.

Om du vill konfigurera en lista över tillåtna IP-adresser går du till `HKLM\SOFTWARE\Microsoft\AzureMfa` och konfigurerar följande register värde:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sträng | Tom | Ange en semikolonavgränsad lista med IP-adresser. Inkludera IP-adresserna för datorer där tjänst begär Anden kommer, som NAS/VPN-servern. IP-intervall och undernät stöds inte. <br><br> Till exempel *10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> Den här register nyckeln skapas inte som standard av installations programmet och ett fel meddelande visas i AuthZOptCh-loggen när tjänsten startas om. Felet i loggen kan ignoreras, men om register nyckeln skapas och lämnas tom om den inte behövs returneras inte fel meddelandet.

När en begäran kommer från en IP-adress som finns i `IP_WHITELIST`hoppas tvåstegsverifiering. IP-listan jämförs med den IP-adress som anges i attributet *ratNASIPAddress* för RADIUS-begäran. Om en RADIUS-begäran kommer utan attributet ratNASIPAddress, loggas följande varning: ”P_WHITE_LIST_WARNING::IP lista över tillåtna ignoreras eftersom käll-IP saknas i RADIUS-begäran i NasIpAddress attribut”.

## <a name="next-steps"></a>Nästa steg

[Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
