---
title: Konfigurera principer för innehålls skydd med hjälp av Azure Portal | Microsoft Docs
description: Den här artikeln visar hur du använder Azure Portal för att konfigurera principer för innehålls skydd. Artikeln visar också hur du aktiverar dynamisk kryptering för dina till gångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978210"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Konfigurera principer för innehålls skydd med hjälp av Azure Portal

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)
>

 Med Azure Media Services kan du skydda dina medier från den tid det lämnar datorn genom lagring, bearbetning och leverans. Du kan använda Media Services för att leverera ditt innehåll krypterat dynamiskt med Advanced Encryption Standard (AES) med hjälp av 128-bitars krypterings nycklar. Du kan också använda den med common Encryption (CENC) med hjälp av PlayReady och/eller Widevine Digital Rights Management (DRM) och Apple FairPlay. 

Media Services tillhandahåller en tjänst för att leverera DRM-licenser och AES-rensa nycklar till auktoriserade klienter. Du kan använda Azure Portal för att skapa en auktoriseringsprincip för nyckel/licens för alla typer av krypteringar.

Den här artikeln visar hur du konfigurerar en innehålls skydds princip med hjälp av portalen. Artikeln visar också hur du använder dynamisk kryptering på till gångar.

## <a name="start-to-configure-content-protection"></a>Börja konfigurera innehålls skydd
Gör så här för att använda portalen för att konfigurera globalt innehålls skydd genom att använda ditt Media Services-konto:

1. I [portalen](https://portal.azure.com/)väljer du ditt Media Services-konto.

1. Välj **inställningar** > **innehålls skydd**.

    ![Innehållsskydd](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Auktoriseringsprincip för nyckel/licens
Media Services stöder flera olika sätt att autentisera användare som gör nyckel-eller licens förfrågningar. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste sedan uppfylla principen innan nyckeln/licensen kan skickas till den. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning.

Du kan använda portalen för att skapa en auktoriseringsprincip för nyckel/licens för alla typer av krypteringar.

### <a name="open-authorization"></a>Öppen auktorisering
Öppen begränsning innebär att systemet levererar nyckeln till alla som gör en nyckel förfrågan. Den här begränsningen kan vara användbar i test syfte. 

### <a name="token-authorization"></a>Token-auktorisering
Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder tokens i formaten simple web token (SWT) och JSON Web Token (JWT). Media Services tillhandahåller inte en STS. Du kan skapa en anpassad STS eller använda Azure Access Control Service för att utfärda token. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Om token är giltig och anspråk i token matchar de som kon figurer ATS för nyckeln (eller licensen), returnerar tjänsten Media Services Key Delivery den begärda nyckeln (eller licensen) till klienten.

När du konfigurerar den token-begränsade principen måste du ange primär verifierings nyckel, utfärdare och mål grupps parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Mål gruppen (kallas ibland omfång) beskriver syftet med den token eller resurs som token tillåter åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

![Auktoriseringsprincip för nyckel/licens](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licensmall
I licens mal len för PlayReady anges de funktioner som är aktiverade på din PlayReady-licens. Mer information om licens mal len för PlayReady finns i [Översikt över Media Services PlayReady-licens mal len](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Ickebeständig
Om du konfigurerar en licens som ej beständig är den lagrad i minnet endast när spelaren använder licensen.  

![Beständigt innehålls skydd](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanent
Om du konfigurerar en licens som beständigt sparas den i beständig lagring på klienten.

![Permanent innehålls skydd](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licensmall
Widevine licens mal len anger de funktioner som är aktiverade för dina Widevine-licenser.

### <a name="basic"></a>Basic
När du väljer **Basic**skapas mallen med alla standardvärden.

### <a name="advanced"></a>Advanced
Mer information om Widevine Rights-mall finns i [Översikt över Widevine-licens mal len](media-services-widevine-license-template-overview.md).

![Avancerat innehålls skydd](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-konfiguration
Om du vill aktivera FairPlay-kryptering väljer du **Fairplay-konfiguration**. Välj sedan **appens certifikat** och ange den **hemliga nyckeln**. Mer information om konfiguration och krav för FairPlay finns i [skydda ditt HLS-innehåll med Apple Fairplay eller Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![FairPlay-konfiguration](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Använd dynamisk kryptering på din till gång
Om du vill dra nytta av dynamisk kryptering kodar du käll filen till en uppsättning MP4-filer med anpassningsbar bit hastighet.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Välj en till gång som du vill kryptera
Om du vill se alla dina till gångar väljer du **inställningar** > **till gångar**.

![Till gångar-alternativ](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Kryptera med AES eller DRM
När du väljer **kryptera** för en till gång visas två alternativ: **AES** eller **DRM**. 

#### <a name="aes"></a>AES
Kryptering med klar text nyckel för AES är aktiverat på alla strömnings protokoll: Smooth Streaming, HLS och MPEG-streck.

![Krypterings konfiguration](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. När du har valt **DRM**ser du olika innehålls skydds principer (som måste konfigureras av den här punkten) och en uppsättning strömnings protokoll:

    a. **PlayReady och Widevine med MPEG-streck** krypterar dynamiskt din MPEG-Tanks-ström med PlayReady och Widevine DRM: er.

    b. **PlayReady och Widevine med MPEG-streck + Fairplay med HLS** dynamiskt kryptera din MPEG-tank strecks ström med PlayReady och Widevine DRM: er. Med det här alternativet krypteras även dina HLS-strömmar med FairPlay.

    c. **PlayReady med Smooth Streaming, HLS och MPEG-streck** dynamiskt krypterar Smooth Streaming-, HLS-och MPEG-streck-strömmar med PlayReady DRM.

    d. **Widevine endast med MPEG-streck** krypterar ditt MPEG-streck dynamiskt med Widevine DRM.
    
    e. **Fairplay endast med HLS** KRYPTERAr HLS-dataströmmen dynamiskt med Fairplay.

1. Om du vill aktivera FairPlay-kryptering väljer du **Fairplay konfiguration**på bladet **Content Protection globala inställningar** . Välj sedan **appens certifikat**och ange den **hemliga nyckeln**.

    ![Krypterings typ](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. När du har gjort krypterings valet väljer du **tillämpa**.

>[!NOTE] 
>Om du planerar att spela upp en AES-krypterad HLS i Safari, se blogg inlägget [krypterade HLS i Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

